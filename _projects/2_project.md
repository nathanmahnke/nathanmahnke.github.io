---
layout: page
title: Lavaboy and Icegirl
description: Fireboy and Watergirl remake using JavaScript
img: assets/img/FireBoyandWaterGirlMainScreen.png
importance: 2
category: Professional
giscus_comments: false
---
This is a faithful recreation of the classic flash game Fireboy and Watergirl created using javascript and html.
<div class="row">
    <div class="col-sm mt-2 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FireBoyandWaterGirlMainScreen.png" title="title screen" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-2 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/FireBoyandWaterGirlLevelOne.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Above, you can see screenshots from the main menu and the game's first level.
</div>

The game works by leveraging an html canvas object as a container for the game's assets. Under the hood so-to-speak, a game engine class manages game-logic. It controls three main facets of the game. Those being; time, player-input, and asset-rendering. The entirety of the code for the game engine can be found [here](https://github.com/philtki/Lavaboy-and-Icegirl/blob/main/gameengine.js), though it is not necessary to explain it line-by-line. 

- Time in the game is tracked by a java script timer started when the game begins. Any class within the game's code can query the game engine for the current tick.
- Player-input is handled via a listener attached to the html canvas. Keyboard inputs are mapped to booleans representing the cardinal directions for each player-character (up/north being the jump key and down/south being used to drop through platforms).
- Finally, asset-rendering is implemented using a FIFO array of entity objects. On any given tick, the game engine updates the next tick, updates the entity list, then draws all entities on the list. Entities, upon creation or update, can be marked for removal. During the next game engine update, they are removed from the entity list and are thus not rendered on the next frame.

The idea of the game is to control Fireboy and Watergirl in an attempt to have them reach their respective doors. However, there are levels to be pulled and boxes to be pushed to allow traversal throug the levels to add complexity to the journey. There is also the added mechanic of color matching and color avoidance. Watergirl, the blue character, is made of water and can pass through the blue liquid pools (water) with ease, while she evaporates if she touches the red liquid pools (lava). As a bonus, the players can increase their scores by collecting gems along their journies, the twist being that the player-characters can only collect their corresponding gems. This leads to an engaging game of puzzle solving in order to reach the goal and collect as many gems along the way as possible.

The entire repository for the game can be found [here](https://github.com/philtki/Lavaboy-and-Icegirl/tree/main).