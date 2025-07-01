---
layout: default
title: Portfolio
permalink: /portfolio/
description: My portfolio, containing both my university projects and personal experiments!
---

# **LiTetris**
> Summer 2025

<img src="/assets/images/litetris2.png" style="height:400px;margin-left:15px;float:right">
<img src="/assets/images/litetris.png" style="height:400px;margin-left:15px;float:right">

In order to improve my C++ programming skills, I decided that I wanted to try and program a game from scratch in the language. I re-created Tetris in C++ using the [SFML](https://www.sfml-dev.org/) graphics library. SFML is a multimedia graphics library built on OpenGL that (amongst other things) provides functions for creating windows, reading inputs and drawing shapes. I used this library to render a front-end for the game, and coded the back end functionality in base C++.

This project helped me to further understand C++ as a language, as well as manage project file structure and using CMake templates to configure builds. The Github repository for LiTetris can be found [here](https://github.com/AllieWhoops/Litetris).

## **Demo Video**
<iframe width="560" height="315" src="https://www.youtube.com/embed/-QhqoLjAuZ8?si=68P2N4my0hmTldzN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<hr/>

# **CoopRPG**
> 2024-2025

<img src="/assets/images/coop 1.png" style="width:400px;height:225px;margin-right:15px;">
<img src="/assets/images/coop 2.png" style="width:400px;height:225px;margin-left:15px;float:right">

As part of my final year of my computer science degree, I had to undertake a project across the year. The prompt I chose to base this project on was 'a networked multiplayer game', as it aligned with my interest in the game development industry. The game I created was a turn-based RPG with roguelike elements (namely procedural level design and death resulting in a game-over state) for up to four players. To develop it, I used the Unity engine, writing scripts in C#. This helped me to gain a strong understanding of development and workflows in Unity, and get to grips with Unity's [Netcode for GameObjects](https://docs-multiplayer.unity3d.com/netcode/current/about/) library.

The premise of the game is that the players take on the role of chickens on a farm, and they must brave the dangers of the farm (like rats, fleas and foxes) to reach the barn (a paradise where there are endless seeds to eat). They progress through levels and eventually reach the farmer, who is the final boss that guards the barn. There are four player classes that represent the four classic fantasy archetypes: the warrior (an armoured fighter), the mage (a wizard), the shadow (a rogue) and the cleric (a priest). Each of these classes have different abilities that allow them to contribute to combat in different ways.

## **Demo Video**
<iframe width="560" height="315" src="https://www.youtube.com/embed/KEmjXTpYyY4?si=ZJ-vmfvgzwHD6cB3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<hr/>

# **Study Leave**
> Winter 2024

<img src="/assets/images/study leave 1.png" style="width:400px;height:225px;margin-right:15px;">
<img src="/assets/images/study leave 2.png" style="width:400px;height:225px;margin-left:15px;float:right">

As a group project in my module on VR, graphics and games, I worked on an educational puzzle game with four other students. The game was coded in C# and used the Unity game engine. We created several computer-science-themed puzzles involving skills like binary-hexadecimal conversion and bit shifting, and placed them in an environment based on one of the buildings on the university campus.

My role was both puzzle design and implementation of the game's inventory system, required for players to pick up keys and useful items like torches. The project received a high grade, and the university faculty requested to use the project as a display to prospective students at university open days.

The game used Unity's HD render pipeline to allow for immersive and spooky graphics despite the low polygon count of the objects designed. This atmosphere helped contribute to the 'escape-room' feel we were trying to provide.


<hr/>

# **Brooklyn Nine-Nine Virtual World**
> Autumn 2024

<img src="/assets/images/b99 1.png" style="width:400px;height:225px;margin-right:15px;">
<img src="/assets/images/b99 2.png" style="width:400px;height:225px;margin-left:15px;float:right">

As an assignment for my VR, graphics and games module, I was tasked with creating a virtual building in Unity. I decided to recreate the precinct office from the sitcom *Brooklyn Nine-Nine*. I used Unity's [ProBuilder](https://docs.unity3d.com/Packages/com.unity.probuilder@6.0/manual/index.html) library to manipulate basic shapes to model the building and included a number of additional interaction features to the world with C# scripts, notably a microwave and elevator starting area. I also played around with Unity's lighting settings to properly emulate the building's interior light, as well as the setting sun on the skyline.

This project was my first time using the Unity Engine to make a 3D enviroment, and helped me to understand how to rapidly prototype levels and other 3D spaces in the engine. It was also useful to familiarise myself with the lighting and rendering parts of Unity that I hadn't interacted with much before.

## **Demo Video**
<iframe width="560" height="315" src="https://www.youtube.com/embed/bua7jk6IJOE?si=Y8EoQR87v61Xqjtc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<hr/>

# **Wizardle**
> Spring 2024

<img src="/assets/images/wizardle 1.png" style="height:250px;float:left;margin-right:15px">
<img src="/assets/images/wizardle 2.png" style="height:250px;margin-right:15px;float:left">

For my Java Programming module at university, I was asked to make an app using Java and Android Studio. I decided to combine my interests in game development and Dungeons & Dragons to produce a daily puzzle game in the vein of *Wordle*. The game provides the player with a series of clues towards a spell from D&D's 5th Edition. The player has a total of 5 guesses they can make before they lose, and the game uses Google Firebase to handle user accounts that track a player's win streak.

This project helped me improve my familiarity with the Java language and object-oriented design. It also taught me about design considerations to be made for non-desktop environments (like a phone or tablet).
