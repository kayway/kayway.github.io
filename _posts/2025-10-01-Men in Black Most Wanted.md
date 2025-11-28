---
title: "Men in Black: Most Wanted"
classes: wide
header:
  video:
    id: 9Z7BhJ8u_Ko
    provider: youtube
author_profile: false
sidebar:
  - title: "Genre"
    image: "/assets/images/MIB-Most-Wanted.png"
    text: "VR, Action, First Person"
  - title: "Platform"
    text: "Meta Quest 2/3"
categories:
  - Game
  - Work
tags:
  - Unity
  - C#
  - VR
---

# Overview
## Role - Gameplay Programmer (Coatsink)

# Key Contributions
- Implemented systems preventing players from leaning into out-of-bounds areas, resolving major edge-case exploits.
- Fixed several VR locomotion issues, including getting stuck on geometry and handling moving platforms.
- Integrated SFX and VFX triggers, ensuring audio/visual events fired at correct gameplay timings.
- Resolved widespread localisation issues, including broken UI layouts and missing translations.
- Developed a Tutorial UI menu, including logic for tracking and saving viewed tutorials.
- Implemented a Language Selection menu with persistent settings.

# Gameplay Systems
I implemented logic to prevent players from bypassing the playspace boundaries, including lean-detection and collision checks tailored for VR. 
I also addressed locomotion issues such as getting stuck on slopes or clipping through moving platforms by refining movement handling and collision resolution.

# UI/UX
I rebuilt multiple UI screens, including the Tutorial Menu which tracks player progress and writes data into the game’s save system. 
I also ensured localisation-safe UI layouts, fixing text overflow, alignment problems, and untranslated fields throughout the game.

# Audio Integration
Worked with the audio and VFX teams to ensure key sequences fired their effects reliably and at the correct in-game moments. 
This included hooking gameplay event triggers to sound cues, particle effects, and environmental interactions.

# Development Infastructure
To support collaboration, I set up and maintained a Perforce server for the project, including depot structure, user permissions, and workspace mappings. This allowed the team to iterate smoothly even under the 1-week time limit.

# Localisation Support
Performed extensive debugging of localisation issues across multiple languages. 
Adjusted UI prefabs, text containers, and sizing logic to ensure all languages rendered correctly.

# Final Thoughts
Worked closely with designers, audio specialists, and producers to deliver timely fixes during a fast-paced VR production. 
Took ownership of several systems, proactively identifying broken behaviours, proposing solutions, and implementing fixes with minimal turnaround time.