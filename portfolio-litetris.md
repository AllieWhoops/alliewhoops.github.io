---
layout: default
title: LiTetris
permalink: /portfolio/litetris/
description: An overview of my recreation of Tetris in C++ using SMFL, called LiTetris!
---

# Background
As an exercise to improve my familiarity with C++ as a language, I decided to create a game using the language. After some consideration, I settled on Tetris as a good starting point. I had used the SDL2 graphics library in my 1st year at university, but decided to use SFML instead for this project as it claimed to be 'object-oriented SDL'. It seemed to fulfil all of the criteria I was looking for: relatively low-level (at least compared to something like Unreal Engine), easy to get started with, and designed to be used with C++ over C (as I wanted to get more comfortable with C++'s syntax as a primary goal).

After toying around with creating windows and handling inputs (and making a window that runs away from your mouse cursor), I got started figuring out how to implement Tetris.

# Requirements & Design
### Functional Requirements
- The game should create a window that displays the game board, which should be a 10x20 grid with 2 extra 'spawn rows' at the top.
- Pieces should appear one at a time in the spawn rows, and fall slowly over time until they land on the bottom of the grid or on another piece.
- The player should be able to rotate pieces as well as move them down, left and right.
- The game should end when a piece settles on the two rows above the grid.
- The pieces should be generated in a '7-bag', where the game creates a bucket containing one of every piece type, then randomly selecting pieces from the bucket until it is empty.

### Non-Functional Requirements
- The game should be relatively simple (no fancy features like holding, wall-kicking, etc.). This is an exercise to improve familiarity with C++, not an attempt to perfectly recreate modern Tetris games.
- The game should responsive to player input.
- The game should have a small file size (less than 10MB).
- The game should only use functions from core C++ and SFML libraries.

### Design Assumptions
Going into this project, I had a list of basic assumptions that would guide my development:
- The core of the game is the grid, which would be represented in the back end by a 2-dimensional array.
- I would need classes for the grid, each grid space, the shapes that fall on the grid, and a bag that holds upcoming shapes.
- The game would handle all of the mechanics in the back-end, like updating placement of shapes as well as clearing and shifting lines.
- The front end (the SFML-created window) will be used to take player inputs and render the results of the back-end changes on the screen.

# Implementation

### The Grid
The default Tetris grid is divided into squares, and is drawn to appear 10 squares wide by 20 squares tall. Shapes spawn just above the visible grid, into rows 21 and 22. This means we need a 10x22 array to hold every piece. After some research and testing, the clear choice for this data structure appeared to be two nested `std::array`s. I used this over a C-style array (`T[][]`) to allow for direct assignments of sections of the array (this is useful for shifting lines after one is cleared).

### Grid Spaces
The spaces in the grid (the actual values in the arrays) are represented by a wrapper class for SFML's `sf::Color` class, called **Gridspace**. This class is initialised to be transparent (`sf::Color::Transparent`), and has a method to get an identifying character representation of the space (used for testing in the console).

### Shape Movement and Line Clearing
When a shape needs to move (for example, being rotated or lowered), the game first performs a check to ensure that the new position is valid. This is done by passing through the new coordinates as an array, meaning that this method can be used for every type of movement by just changing the method that passes in the parameters. It iterates through each point, checking if any are not transparent, and return false if any non-transparent points are **not** inside of the shape itself.

```
bool Grid::CheckMoveLegal(std::array<std::array<int, 2>,4> newCoords, Tetris::Shapes::Shape thisShape){
    // If any space is not transparent, returns false
    int problemPoints = 0;

    for(auto point : newCoords){

        std::cout << point[0] << " " << point[1] << "\n";

        if(point[0] >= GRID_WIDTH || point[0] < 0){
            std::cout << "X coord not in range";
            return false;
        }
        if(point[1] >= GRID_HEIGHT || point[1] < 0){
            std::cout << "Y coord not in range" << "\n";
            return false;
        }

        if(arrGrid[point[0]][point[1]].col != sf::Color::Transparent){
            problemPoints++;

            for(auto myPoint : thisShape.GetCoords()){
                if(point == myPoint){
                    problemPoints--;
                }   
            }  
        }
    }

    if(problemPoints > 0){
        return false;
    }

    return true;
}
```

In Tetris, the shapes rotate around a specified centre point. For most pieces, this is the centre of one of the 'middle squares' (see image). This makes it relatively trivial to apply 2 transformations to the shape's coordinates using integer matrix multiplication. 

```
std::array<std::array<int, 2>, 2> rotMatrix;

if(clockwise){ // Position number & rotation matrix set based on rotation direction
    rotMatrix = {
        {
            {0, 1},
            {-1, 0}
        }
    };
}
else{
    rotMatrix = {
        {
            {0, -1},
            {1, 0}
        }
    };
}

for(int i = 0; i < 4; i++){ // Performs 90* rotation on the coordinates with the power of matrices
    int x = coords[i][0] - origin[0]; // origin is the coordinates of the centre of rotation
    int y = coords[i][1] - origin[1];

    coords[i][0] = rotMatrix[0][0] * x + rotMatrix[1][0] * y + origin[0];
    coords[i][1] = rotMatrix[0][1] * x + rotMatrix[1][1] * y + origin[1];
}
```

<figure style="margin-left:15px;float:right">
    <img src="/assets/images/SRS-pieces.png" style="height:250px;margin-left:30px">
    <figcaption><i>Image courtesy of TetrisWiki</i></figcaption>
</figure>
The O piece (yellow) and the I piece (light blue) have a centre of rotation on the line between two squares, which makes things more complicated. For the O piece, this actually doesn't matter since it is rotationally symmetrical and therefore rotating it does nothing. However, this means another solution is still required for I pieces. In the end, I decided that the most efficient solution would be to hardcode the rotations for I pieces, since there are only four permutations. This does reduce the elegance of the code somewhat, but it worked and allowed me to move on to making the rest of the game.

### The Front-End
As discussed above, the front end of the program is mostly concerned with taking player inputs and drawing a representation of the grid on the screen. It also handles the game's ticks, which happen every 0.6 seconds. If a piece is stationary for a whole tick, it will drop by one row, and if the bottom of the piece is touching something for a tick, it is locked in place and a new controllable piece spawns.

```
else if(const auto* keyPressed = event->getIf<sf::Event::KeyPressed>()){ // Checks if a key is pressed down
    if(keyPressed->scancode == sf::Keyboard::Scancode::R){
        if(myGrid.CheckMoveLegal(newShape.TryRotate(), newShape)){ // Tests if piece can rotate before performing rotation
            myGrid.ClearSpaces(newShape.GetCoords()); // clears old occupied spaces
            newShape.Rotate(); // rotates stored coordinates of shape
            myGrid.AddShape(newShape); // adds shape to the 2d array that represents the grid
        }
        else{
            std::cout << "Illegal\n";
        }  

        lastTickTime = time(NULL); // reset last tick to now
    }
    else if...
}
```

Once any player input is handled, the game checks to see if the current active piece can be moved down a row. If it can, the piece is moved down. If it cannot, the piece is therefore in its final position. If this final position is in the two rows above the grid walls, then the player has losed and the game is over. After all of the logic has concluded, the game draws the grid walls and all of the filled spaces on the screen, before the logic loop restarts for the next frame.

```
if(difftime(now, lastTickTime) > gameTickLength){ // DoGameTick
    lastTickTime = now;
    if(myGrid.CheckMoveLegal(newShape.TryMove(Tetris::Shapes::Direction::Down), newShape)) // checks if moving downwards is possible
    {   // shape falls one row
        myGrid.ClearSpaces(newShape.GetCoords());
        newShape.Move(Tetris::Shapes::Direction::Down);
        
        myGrid.AddShape(newShape);
    }
    else{ // shape is in final position
        if(!myGrid.CheckIfGameOver(newShape)){ // checks if shape has finished in spawn rows
            myGrid.AddShape(newShape); // draws new position of shape
            newShape = myBag.GetShape(); // pull new shape from the 7-bag
            myGrid.DoLineClears(); // check if any lines have been filled and should be cleared
        }
        else{ // Game has ended and game over message can be displayed
            isGameOngoing = false;
        }
    }
}
```

# Skills Used
- C++ Programming
- Object-oriented thinking and design
- Debugging in Visual Studio using breakpoints
- Git Version Control

# Summary
<img src="/assets/images/litetris2.png" style="height:250px;margin-right:15px;float:right">
<img src="/assets/images/litetris.png" style="height:250px;margin-right:15px;float:right">
This project helped me to further understand C++ as a language, as well as manage project file structure and using CMake templates to configure builds. The Github repository for LiTetris can be found [here](https://github.com/AllieWhoops/Litetris).

I used this library to render a front-end for the game, and coded the back end functionality in base C++.

## Demo Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/-QhqoLjAuZ8?si=68P2N4my0hmTldzN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
