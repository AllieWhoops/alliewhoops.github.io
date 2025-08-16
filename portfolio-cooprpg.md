---
layout: default
title: CoopRPG
permalink: /portfolio/cooprpg/
description: An overview of my degree project, a networked multiplayer turn-based RPG called CoopRPG!
---


# Background
As part of my final year of my computer science degree, I had to undertake a project across the year. The prompt I chose to base this project on was 'a networked multiplayer game', as it aligned with my interest in the game development industry. The game I created was a turn-based RPG with roguelike elements (namely procedural level design and death resulting in a game-over state) for up to four players. To develop it, I used the Unity engine, writing scripts in C#. This helped me to gain a strong understanding of development and workflows in Unity, and get to grips with Unity's [Netcode for GameObjects](https://docs-multiplayer.unity3d.com/netcode/current/about/) library.

The premise of the game is that the players take on the role of chickens on a farm, and they must brave the dangers of the farm (like rats, fleas and foxes) to reach the barn (a paradise where there are endless seeds to eat). They progress through levels and eventually reach the farmer, who is the final boss that guards the barn. There are four player classes that represent the four classic fantasy archetypes: the warrior (an armoured fighter), the mage (a wizard), the shadow (a rogue) and the cleric (a priest). Each of these classes have different abilities that allow them to contribute to combat in different ways.

# Requirements & Design
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

### Design Assumptions

 
# Implementation

# Skills Used

# Summary
<img src="/assets/images/coop 1.png" style="width:390px;height:225px;margin-right:15px;">
<img src="/assets/images/coop 2.png" style="width:390px;height:225px;margin-left:15px;float:right">

## Demo Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/KEmjXTpYyY4?si=ZJ-vmfvgzwHD6cB3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
