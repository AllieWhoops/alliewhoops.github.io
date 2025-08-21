---
layout: default
title: CoopRPG
permalink: /portfolio/cooprpg/
description: An overview of my degree project, a networked multiplayer turn-based RPG called CoopRPG!
---
# Background
As part of my final year of my computer science degree, I had to undertake a project across the year. The prompt I chose to base this project on was 'a networked multiplayer game', as it aligned with my interest in the game development industry. The game I created was a turn-based RPG with roguelike elements (namely procedural level design and death resulting in a game-over state) for up to four players. To develop it, I used the Unity engine, writing scripts in C#. This helped me to gain a strong understanding of development and workflows in Unity, and get to grips with Unity's [Netcode for GameObjects](https://docs-multiplayer.unity3d.com/netcode/current/about/) library.

The premise of the game is that the players take on the role of chickens on a farm, and they must brave the dangers of the farm (like rats, fleas and foxes) to reach the barn (a paradise where there are endless seeds to eat). They progress through levels and eventually reach the farmer, who is the final boss that guards the barn. There are four player classes that represent the four classic fantasy archetypes: the warrior (an armoured fighter), the mage (a wizard), the shadow (a rogue) and the cleric (a priest). Each of these classes have different abilities that allow them to contribute to combat in different ways.

# Requirements
### Functional Requirements
- The game must feature networked multiplayer gameplay.
    - The game uses a client-host model, where one game instance acts as both a client and the server.
- The game needs to be able to handle connecting players together using Unity Relay.
- The game needs to handle player inputs client-side, and make any changes that don't affect the game state occur only on that client.
- The game should facilitate player-to-player communication, through text communication.
- The game should be able to be resumed in the event of a player disconnect.


### Non-Functional Requirements
- The game should be suitable for players with slower internet speeds.
- The game should have four player classes to facilitate playstyle customisation.
- The enemies should have different behaviours based on their intelligence level.

# Design
### UML Class Diagrams
This project has a lot of moving parts in many of the scenes, with many different classes interacting with each other. In order to keep the functionality and interactions clear, I created class diagrams for each of the major scenes in the game.

#### Fight Scene
<a href="/assets/images/Fight Scene Class Diagram.png" target="_blank"><img src="/assets/images/Fight Scene Class Diagram.png" style="width:600px;display:block;margin:auto"></a>

The fight scene is responsible for handling the commands given by players during combat and displaying the encounter on the screen. The primary loop (which starts with action selection, transitions to action execution and turn cleanup, and then returns to action selection) is managed by the FightSceneHandler on the host’s instance of the game, as the host is the instance that also stores character and enemy data.

The client instances transmit the players’ commands, which get added to the ‘initiative list’ (a list of actions and related information such as user, target and action priority). Once all players have submitted actions (or the one-minute timer has expired), the FightSceneHandler requests an action from each Enemy before beginning action execution.

The PlayerConnectionHandler class is responsible for holding player information, including their entered name and character sheet. This character sheet is a container for various information used in the combat encounter, such as character statistics and special abilities. Each attack and ability (non-attacking actions that provide various boosts to characters) is stored as an AttackProfile or AbilityProfile respectively. These implement Unity’s INetworkSerializable interface, meaning they can be sent over the network between the host and clients.

#### 'Load Game' Lobby
<a href="/assets/images/Load Lobby Class Diagram.png" target="_blank"><img src="/assets/images/Load Lobby Class Diagram.png" style="width:600px;display:block;margin:auto"></a>

The ‘load game’ lobby is responsible for connecting players to a game that is loading a previously saved game (in the instance of a disconnect, for example). It needs to allow players to select which character they are playing and display this information to all players. When a player chooses which character is theirs, their character sheet is overwritten with the stored one.

The LoadLobbyController takes the list of loaded CharacterSheets from the SaveGameHandler and presents them to the players. Each player in turn gets to select one of the sheets, which is then copied to that player’s PlayerConnectionHandler serverside. It also changes the playerName NetworkVariable to the saved character name.

### Level Generation Design
There are several approaches that I considered for the level generation in the game. The requirement is that the game randomly generates a number of levels that players choose between, which can be implemented in a number of ways.

The first way this could be done is by generating a map where each level is connected to future levels, so the players can choose a path knowing what lies ahead. This is similar to games like *Slay the Spire*, which populates a grid with a bucket of rooms and generates connections between the rooms. 

An alternative approach is taken in *Hades* by Supergiant Games. *Hades* does not create an elaborate branching tree of levels to show to the player and instead simply displays a set of three rooms for the player to choose between, with no knowledge of what lies beyond.

This project uses the approach taken by Hades, as it removes the need for complex branching trees, which would have taken up a large amount of the project’s scope to properly implement.

# Implementation

### Saving & Loading
Due to the inherent instability of online gaming (where players can be disconnected due to any number of network issues), it was important that this project have some way to resume a game in the event of a disconnect.

The game needs to store the current state of the game, including player names and classes, the current level, and the generated bucket of rooms. I used the Newtonsoft JSON library to store the information in a series of JSON objects. This can be used alongside the C# System.IO and Unity Application libraries to save to the ‘persistent data path’, which varies depending on the user’s operating system. On Windows devices, this path is found in the `AppData/LocalLow` folder.

The game uses a persistent GameObject called the save game handler to copy the character sheet class from each of the players’ player connection handler, as well as the room bucket and level number, and saves each as a .dat file within a folder. The game’s start time is used as the folder name. It does this whenever the players load into the level selection screen, so the game can be resumed from before the level. The data is only saved on the host, as the character data for each player is stored exclusively server-side.

```
// Method for saving game data to the disk.
    public void SaveAll(string saveName)
    {
        
        List<string> saveString = new List<string>(); // List of strings that will hold the data to be saved.
        List<CharacterSheet> sheets = GetCharacterSheets(); // Obtains the character sheets of all current players.

        // Serialises the list of character sheets into JSON and stores it as a string
        saveString.Add(JsonConvert.SerializeObject(sheets, Formatting.Indented, new JsonSerializerSettings
        {
            ReferenceLoopHandling = ReferenceLoopHandling.Ignore
        }));

        List<RoomType> rooms = MapNodesGenerator._roomsBucket; // Stores the current state of the room bucket
        saveString.Add(JsonConvert.SerializeObject(rooms, Formatting.Indented)); // Serialises the map bucket as JSON and stores it as a string

        saveString.Add(MapNodesGenerator._currentRow.ToString()); // Gets the current level the players are on and stores it as a string

        string fileName = Application.persistentDataPath + $"/{saveName}"; // Creates a string containing the location the file should be store.
        if (!Directory.Exists(fileName))
            Directory.CreateDirectory(fileName); // Creates directory for the save file if it doesn't exist

        // Stores each part of the save data in a different, appropriately named file.
        File.WriteAllText(fileName + "/sheets.dat", saveString[0]);
        File.WriteAllText(fileName + "/roombucket.dat", saveString[1]);
        File.WriteAllText(fileName + "/levelNo.dat", saveString[2]);
    }
```

### Fight Scene
The most complex aspect of the game flow is the fight scene. This is where the turn-based combat between the players and enemies is executed. Much of the flow of logic is controlled by a GameObject called the FightSceneHandler. The following flowchart shows how the loop continues until either the players or the enemies are defeated.

<a href="/assets/images/Fight Scene Flowchart.png" target="_blank"><img src="/assets/images/Fight Scene Flowchart.png" style="width:600px;display:block;margin:auto"></a>

When the scene is first loaded, each client requests information on its player’s various abilities. The server returns these and sends each client information on which enemies to display on the player’s screen. Once every connected client receives this data, the server’s FightSceneHandler begins the turn timer. This is a 60 second countdown during which each player can select their move for the turn. If the timer expires without a player selecting a move, they are automatically set to defend and do nothing else.

When a player clicks a button to select a move, it sends an RPC (remote procedure call, a way of invoking a method over the network) to the server with the selected action, the target of the attack (only relevant if the attack had a single target), the type of target (self, single ally, single enemy, all allies or all enemies) and the speed of the character using the action (which determines the order of actions). It sends this as an ActionContainer object, which is added to a list of each submitted action held by the server’s FightSceneHandler. Once the timer expires or all players have submitted actions, the FightSceneHandler calls the MakeMove method for each of the enemies (which generates the actions they will take during the round) and adds those actions to the list. The list is then sorted, first by the priority of each move (some moves have increased priority which makes them move before other moves, however these are usually less damaging than other attacks), and then by the speed of each character in the fight.

The server’s FightSceneHandler iterates through the sorted list and executes each action in turn, applying effects such as damage or powering up the targeted characters. It also sends RPCs to every client to make them display text describing each action, so the players can follow along with the turn and understand what is happening. Once each action has been described, the turn ends, and the server’s FightSceneHandler updates its lists of alive player characters and enemies, removes the sprites of defeated characters, and tells the clients of surviving characters to display the combat menu again. The cycle then repeats until every enemy has been defeated, and the players can continue to the next level (or to the victory screen, if it is the final boss fight), or until all the players have been defeated, and a ‘game over’ screen is displayed.

# Skills Used
- C# Programming
- Test Writing
- Git Version Control

# Screenshots
<img src="/assets/images/coop 1.png" style="width:390px;height:225px;margin-right:15px;">
<img src="/assets/images/coop 2.png" style="width:390px;height:225px;margin-left:15px;float:right">

## Demo Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/KEmjXTpYyY4?si=ZJ-vmfvgzwHD6cB3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>