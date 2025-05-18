---
layout: page
title: Dungeon Adventure
description: Completely original dungeon adventure game made using Java
img: assets/img/Knight.png
importance: 2
category: Professional
---

This project brings to life a procedurally generated dungeon-crawling experience, seamlessly blending algorithmic ingenuity with a vibrant graphical interface. At its heart is a custom room-generation engine implemented via two-dimensional arrays: starting from a single “seed” room, the DungeonGenerator class iteratively expands prospective rooms (“P”) into solidified chambers (“S”), ensuring each wave of growth yields between one and three new rooms while safeguarding against overlaps or dead ends. Once the raw layout is built, the generator refines and encodes each room’s cardinal connections into concise binary “room codes,” ready for on-screen rendering.

Complementing the generation logic, a JavaFX‑based GUI system interprets these 2D arrays and maps them to visual assets. Hallways, doorways, and chamber sprites are placed dynamically at calculated pixel coordinates, using mathematical transforms (ceiling and floor operations) to account for varying tile dimensions. The DungeonAdventure class bridges the generator and the UI layer: spawning rooms, hallways, monsters, and the player character with precise offsets to ensure crisp alignment and fluid interaction.

Player mechanics are woven into this framework through event-driven handlers that respond to keyboard inputs. These allow player-character movement, collision detection, and interaction with in‑dungeon entities. Monsters such as Gremlins, Skeletons, and Ogres are placed during generation rounds, leveraging object references stored within each Room instance to determine when and where to render adversaries.

Collaboration played a key role in enriching the game’s combat dynamics. The core dungeon infrastructure and rendering pipeline were architected independently, but combat systems including; hit calculations, turn order, and enemy AI were seamlessly integrated by a colleague, Colton. This allows the player to interact with the enemies found throughout the dungeon.

The source code can be found here [here](https://github.com/Nathan9819/TCSS360_Dungeon_Adventure_Group_Project/)