---
title: "Outta Space"
classes: wide
header:
  teaser: /assets/images/outta-space-banner.png
  video:
    id: Nor2QOBQa-Y
    provider: youtube
author_profile: false
sidebar:
  - title: "Genre"
    image: /assets/images/outta-space-banner.png
    text: 3D, Arcade, Survival, Space, Fast-Paced
  - title: Platform
    text: Windows x64
categories:
  - Personal Projects
tags:
  - Unreal Engine
  - C++
  - Blueprints
  - UMG
---

An game where you need to escape a expanding black hole. Dodge asteroids as you try to reach the end goal before the dark hole engulfs you.
I was responsible for all the asset implementation(Sound, 3D Assets, UI, etc.) and functionality.

<iframe height="175" width="560" src="https://itch.io/embed/3422717?linkback=true&amp;border_width=5&amp;bg_color=000000&amp;fg_color=ffffff&amp;link_color=ad1ab9&amp;border_color=ad1ab9" frameborder="0">&lt;a href=&quot;<a href="https://kayofways.itch.io/outta-space">Outta&quot; class=&quot;redactor-linkify-object&quot;&gt;https://kayofways.itch.io/outta-space&quot;&gt;Outta</a> Space by KayOfWays, Drnisme, Rheba&lt;/a&gt;</iframe>

[Source Code](https://github.com/kayway/OuttaSpace)

# Key Contributions
- Designed and programmed a custom Pawn movement controller in C++ for responsive, smooth character handling.
- Built a dynamic expanding black hole volume that grows over time and influences gameplay tension.
- Implemented all the UI systems(Player HUD, Dialogue UI, Options Screen, etc.)
- Implemented Settings with save functionality.
- Perforce Server setup and maintenence.

# Gameplay Systems
I created a lightweight custom Pawn controller in C++ to focus on responsiveness and clean separation between input, movement logic, and animation triggers. 
The signature mechanic is a dynamically expanding black hole volume that increases tension as the player progresses, implemented through scalable collision logic and timed growth.

# UI/UX
The UI was built entirely using UMG, including the HUD for tracking health, objective progress, and black hole proximity. 
I also implemented pause and game-over flows, along with a simple dialogue system triggered by gameplay events. 
The Options Menu uses a persistent save slot so player settings carry over across sessions.

# Audio Integration
Although I didn’t create the assets, I handled all audio implementation, wiring sound effects and music into gameplay and UI events. 
This helped reinforce feedback and improve the moment-to-moment clarity.

# Development Infastructure
To support collaboration, I set up and maintained a Perforce server for the project, including depot structure, user permissions, and workspace mappings. This allowed the team to iterate smoothly even under the 1-week time limit.

# Development Decisions
Given the tight timeline, I made the choice to implement UI behaviour and input handling in C++ for faster iteration and predictable logic flow. 
This allowed me to focus on building a stable, functional core rather than polishing visuals.

# Final Thoughts
This prototype helped strengthen my rapid prototyping workflow in Unreal, especially around movement systems, UI pipelines, and state handling. It reinforced the value of modular design under time pressure, and gave me practical experience building a small but complete gameplay loop end-to-end.