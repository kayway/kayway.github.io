---
title: "Men in Black: Most Wanted"
classes: wide
header:
  teaser: /assets/images/MIB-Most-Wanted.png
  video:
    id: 9Z7BhJ8u_Ko
    provider: youtube
sidebar:
  - title: Genre
    image: /assets/images/MIB-Most-Wanted.png
    text: Action, Adventure, FPS
  - title: Platform
    text: Meta Quest 2/3
  - title: Technologies
    text: C#, Oculus SDK, Tortoise SVN, Unity
  - title: Skills
    text: VR Development, Locomotion, UI Development, SFX Implementation, VFX Implementation, Localisation
categories:
  - Work Projects
tags:
  - Unity
  - C#
  - VR Development
  - UI Development
---

VR Action Puzzle game, where I was responsible for a variety of features.

# Key Contributions
- Fixed several VR locomotion issues, including getting stuck on geometry and handling moving platforms.
- Integrated SFX and VFX triggers, ensuring audio/visual events fired at correct gameplay timings.
- Resolved widespread localisation issues, including broken UI layouts and missing translations.
- Developed a Tutorial UI menu, including logic for tracking and saving viewed tutorials.
- Implemented a Language Selection menu with persistent settings.

# Locomotion
{% include video id="4wKDrAKxSW8?t=4262" provider="youtube" %}

I implemented logic to prevent players from bypassing the playspace boundaries, including lean-detection and collision checks tailored for VR. 
I also addressed locomotion issues such as getting stuck on slopes or clipping through moving platforms by refining movement handling and collision resolution.

# UI/UX
![Tutorial Menu](/assets/images/TutorialMenuUI.png)

I created multiple UI screens, including the Tutorial Menu which tracks player progress and writes data into the game’s save system. 
I also created a Language select screen using Coatsinks in-house localization system.

# Audio/VFX Implementation
Worked with the audio and VFX teams to ensure key sequences fired their effects reliably and at the correct in-game moments. 
This included hooking gameplay event triggers to sound cues, particle effects, and environmental interactions throughout the game.

# Localisation Support
Performed extensive debugging of localisation issues across multiple languages. 
Adjusted UI prefabs, text containers, and sizing logic to ensure all languages rendered correctly.

# Final Thoughts
Worked a lot with other disciplines on this one, in particular VFX Artists and Sound, ensuring they not only have their events executed at the correct time but also that they have the control they need to tweak things like positioning or event parameters.