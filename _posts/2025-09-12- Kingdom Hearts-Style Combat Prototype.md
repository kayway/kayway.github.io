---
title: Kingdom Hearts-Style Combat Prototype
classes: wide
header:
  teaser: /assets/images/Kingdom Hearts-Style Combat Prototype.png
  video:
    id: u7u7cWItGGc
    provider: youtube
sidebar:
  - image: /assets/images/Kingdom Hearts-Style Combat Prototype.png
  - title: Technologies
    text: C#, Unity, Emerald AI, RPG Combat Framework
  - title: Skills
    text: AI Development, Systems Integration, Prototyping
categories:
  - Freelance Projects
tags:
  - Unity
  - C#
  - AI Development
---

A Unity prototype inspired by Kingdom Hearts, featuring action-combat, crowd-handling, and multiple AI behaviours using two client-provided frameworks.

# Key Contributions
- Implemented player character combat behaviour, integrating two the clients [Emerald AI](https://assetstore.unity.com/packages/tools/behavior-ai/emerald-ai-2025-268519?srsltid=AfmBOoq_8bCrQmpUvKB-G1Wu_C2YB7nwMGjS6OjrTWYlekV3CmILRLeb) framework, and the rpg combat framework.
- Built enemy AI using [Emerald AI](https://assetstore.unity.com/packages/tools/behavior-ai/emerald-ai-2025-268519?srsltid=AfmBOoq_8bCrQmpUvKB-G1Wu_C2YB7nwMGjS6OjrTWYlekV3CmILRLeb), tuned for crowd-handling to prevent overwhelming the player.
- Integrated both frameworks’ health and damage systems, ensuring compatibility and consistent behaviour.

# Combat
Implemented the Rpg combat framework to work with the emerald AI framework by having the Combat framework trigger the emerald AI damage events and let there system handle the data.

# AI Crowd Handeling
They wanted the crowds to behave so they form a circle around the player then randomly a couple of enemies move in to attack. This needed to be a custom implementation that integrated with emerald AI so that it doesn't break its behavior systems.

# Final Thoughts
This involved a lot of reading the two frameworks code and understanding there setup requirements and its api in order to add custom systems and have them all work together.