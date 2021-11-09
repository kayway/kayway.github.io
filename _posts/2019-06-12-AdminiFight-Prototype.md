---
header:
  video:
    id: TuFyN7rfjqM
    provider: youtube
title: "AdminiFight Prototype"
classes: wide
sidebar:
  - title: "Game Details"
    image: "/assets/images/Adminifight-Prototype.jpg"
    text: "Genre: Fighting  Platform: Windows"
categories:
  - Game
tags:
  - Unreal Engine
  - C++
---
<iframe frameborder="0" src="https://itch.io/embed/1133264?bg_color=8e9cd2" width="552" height="167"><a href="https://kayofways.itch.io/adminifight-prototype">AdminiFight-Prototype by KayOfWays</a></iframe>

# Overview
A 3D Action Combat game where you have to defeat all the enemies or collect all the pumpkins in the level. 
This was a prototype for the full release which has been shelved for now after realising the amount of 3D work it would require.
# Features
- Enemy AI: Melee and ranged ai to defeat in order to beat the game.
- Splitscreen multiplayer: Can work together with up to 4 players.
- Melee Combat: Pummel your enemies with your brutal combos.
- Platforming: some obstacles you must use your abilities in creative ways in order to get all the pumpkins.
- Special Abilities: Teleportation and shoot bursts of power to handle multiple enemies.

These where all made by me save from some art assets used.

# Gameplay

Many systems were implemented here for prototyping purposes; these features are:
- Health and Stamina System
- Energy and Ability system
- Cooperative Splitscreen Mode
- Lock on System
- Health and stamina pickups
- Block and Combat System
# AI

There are two types of AI implementied here:
- Ranged AI
- Melee AI 

They both patrol to random points in a radius and will pursue the play using the AI perception system provided by the unreal engine. 
They will also go to the last seen location of the player if they have lost sight off him except for the ranged AI who doesnt chase the player.
# Animation In Unreal
Animation was a very interesting obstacle. Scripted a base montage class in c++ so i could use multiple child montages and utuilize the same functionality to save time.
Implementation of a state mation and performing blends and montages where all very new to me but definately gave me some insight on the hurdles an animator in the 3d space is presented.
Speaking of animation actually animating the character was way more difficult than i first fought implementing not only key frames but different types of curves to controll the flow of the animation.
# Closing Thoughts
It started with me and my friends discussing what attacks we would have in a fighting game and how it would make a really good game. Well i thought that too and wanted to try making it. However i was pretty naive back then as I only just started university in computer science and had no ecxperiance making games. 
So it took a fair few years and even based my dissertation on learning the skills (See [2.5D Fighting game](https://kayway.github.io/game/2.5D-Fighting-Game/)).
However I soon learned that even though i could script and use most of the tools unreal had to offer, that is only the base of the project.
Mostly things on the artistic side but some slightly more technical roles such as implementing animations and blending.
This didnt deter me and I auctually learnt a lot about blender like Sculpting, 3D animation and modeling, UV unwrapping etc.
But eventually I stopped due to it taking way too much time, but i still often look back wanting to finish it which I may do someday, as ive gained more experiance i find i can complete tasks much quicker than before.