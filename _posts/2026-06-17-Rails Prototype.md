---
title: Rails Prototype
classes: wide
header:
  teaser: /assets/images/Rails Prototype.png
  video:
    id: AKn93RFZEbs
    provider: youtube
sidebar:
    - image: /assets/images/Rails Prototype.png
    - title: Technologies
      text: C#, Unity, Cinemachine
    - title: Skills
      text: UI Development, Prototyping, Animation Programming
categories:
  - Freelance Projects
tags:
  - Unity
  - C#
  - UI Development
  - Prototyping
---

A 3D action rail-shooter prototype developed in Unity, created to explore character movement, combat systems, and responsive gameplay mechanics.

# Key Contributions
- Developed a 3D rail-shooter prototype in Unity from concept to playable vertical slice.
- Implemented player movement, camera-based aiming, projectile combat, and collision systems.
- Built gameplay systems including health, damage, scoring, and game state flow.
- Created responsive UI systems with animated health bars, crosshair controls, and gameplay feedback.
- Integrated animations, VFX, and audio to improve combat feel and player feedback.
- Worked from a client brief to rapidly prototype and iterate on gameplay mechanics.

# Rail System
It would take a list of points with its own class with functions like time to point what type of point like eny point requiring to defeat all the enemies before being marked complete or autocomplete.
The the rail system would move the "cart" object between those points, I would then have the player object be the child of the cart allowing movement to the points and player controlled movement with no conflict.

# Player Controller
The controller would simply move to a restricted plane based on the camera forward and would move dependant on the camera view with an user controlled margin.
The alternative would be to detect the collision of the floor and walls but that would introduce possible out of view moments with the player and just make the system more complex for no reason.

# Final Thoughts
This was a fun prototype to work on with a similar setup to Outta Space. It also involved a bit more polish than other integrating sfx vfx, animations and controlling animation states.