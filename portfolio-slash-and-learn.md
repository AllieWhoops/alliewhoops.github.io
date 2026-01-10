---
layout: default
title: Slash & Learn
permalink: /portfolio/slash-and-learn/
description: An overview of my GMTK Game Jam 2025 project, Slash & Learn!
---

# Background
I entered Gamemaker's Toolkit's (GMTK) 2025 game jam as a solo developer in order to improve my game development skills and creative instincts. The jam took place over four days in August, and had the starting prompt of 'loops'. I decided that I wanted to interpret this in the sense of time-loops, where time repeats until the player succeeds at a task.

My initial ideas were a puzzle platformer where the player uses the dead bodies of previous attempts to work through levels, or a hack-and-slash where the player can rewind time to avoid enemy attacks. I decided to go with the second one as it was more interesting to me, and I could better visualise what the gameplay would look like.

The basic concept was inspired by boss fights in games like Final Fantasy XIV, where players learn the mechanics of each encounter by dying and figuring out how to avoid dying next time. The player would have access to some means of reversing the flow of time in the game, undoing any damage that the player takes from enemies.

# Requirements
### Functional Requirements
- The player should be able to use the keyboard to move the character around the arena.
- The player should be able to rewind time in the game, undoing damage and attacks that occurred in the past few seconds.
- The monster should perform a predetermined sequence of attacks, and loop the sequence when it ends.
### Non-Functional Requirements
- The time-rewind animation should replay the past 3 seconds of gameplay with 0.5 second intervals.
- The monster sequence should last at least 30 seconds.
- The player should have three health, which restores if the attack is rewound.

# Design

### Player Character Design
The basic controls of the player character use Unity's InputSystem to take keyboard inputs (W, A, S and D), and applies a linear velocity to the character, moving them at a consistent speed. It also activates animation triggers, so the sprite appears to move as the character does. Initially, I planned to use Unity's CharacterController system to implement movement, but as it was not compatible with rigidbodies (the components that facilitate collisions between objects), I had to adapt.

The player character is also able to attack the enemy by pressing the E key. The character stabs with a sword in the direction they are facing, extending a raycast to detect colliders within the sword's reach. If it collides with an enemy, it is treated as a 'hit' and the enemy takes damage.

### Enemy Design

### UI Design

# Implementation

### Time Manipulation

### ???

# Skills Used
- C# Programming
- Unity Animation
- Git Version Control
- Rapid Prototyping
- Unity Shadergraphs

# Screenshots

# Demo Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/B-z2noWlU5A?si=pRTArhzBcJYM_zbq" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>