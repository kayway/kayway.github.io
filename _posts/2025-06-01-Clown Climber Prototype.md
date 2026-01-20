---
title: Clown Climber Prototype
classes: wide
header:
  teaser: /assets/images/Clown Climber Prototype.png
  video:
    id: tXo_3X8LavQ
    provider: youtube
sidebar:
    - image: /assets/images/Clown Climber Prototype.png
    - title: Technologies
      text: C#, Unity
    - title: Skills
      text: AI Development, Prototyping, Gameplay Development, Systems Integration
categories:
  - Freelance Projects
tags:
  - Unity
  - C#
  - AI Development
  - Prototyping
---

A quirky stealth-climbing prototype involving climbing systems, enemy AI, and comedic abilities like pies, banana peels, and whoopie cushions.

# Key Contributions
- Implemented enemy AI using the Reactive AI Framework.
- Created multiple abilities adding vfx and enemy ai state handeling
- Implemented Stealth mechanics and AI patrol/puruite behaviors

# Enemy AI
Using the Reactive AI package provided by the client, I had to understand and develop custom nodes and use the frameworks behavior tree system.
The AI had several states:
- Patrol/Idle: Would patrol around a set of points you place in the level.
- Pursuit: When the player is spotted would chase the player. If they lose sight will chase to the last seen location
- Cautious: Would look arounfd for a little while looking for the player.
- Laughing/Slipping: A "doormant" state where we handel when the AI should not be able to act. For example, when they see the player slip or throw a pie at another enemy they will be in this state for a set time.

When they compeletely lose sight of the player they will just return to patrolling the points.

# Player Abilities
The player has the following abilities:
- Bananna Peel Throw: Throws a bananna peel that when stepped on causies the player or enemy to slip.
- Pie Attack: Slams a pie in the enemies face, killing them.
- Woopie Cushion: Throws a woopie cushion that explodes.

Whenever an enemy AI sees any of this they would enter their laughing state.
This involved understanding the custom climbing controller the client provided and integrating these to its animation controller.

# Additional Notes
The client initially wanted two climbing frameworks merged but after testing, I found it would involve an extremely over-inflated animation controller with a lot of unused features for minimal gain. 
So I advised using one and just developing from that framework to save development time and improving stability.
This involved a lot of reading again and the character controller in particular was pretty massive making a big read to understand it fully haha. 
I enjoyed the AI framework though it reminded me a lot of unreal's Behavior tree system which made it much easier to work with.