---
header:
  video:
    id: TuFyN7rfjqM
    provider: youtube
title: "AdminiFight Prototype"
classes: wide
sidebar:
  - title: "Genre"
    image: "/assets/images/Adminifight-Prototype.jpg"
    text: "3D, Action, Fighting, Platforming"
  - title: "Platform"
    text: "Windows x64"
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

# Gameplay

Many systems were implemented here for prototyping purposes; these features are:
- Health and Stamina System
- Energy and Ability system
- Cooperative Splitscreen Mode
- Lock on System
- Health and stamina pickups
- Block and Combat System

# AI
![Behavior Tree](/assets/images/Adminifight-Behaviortree.png)
There are two types of AI implemented here:
- Ranged AI: Fires Homing Projectiles at the player
- Melee AI: Chases the player and Attacks the player and also can surround the player if there in a group.

Made use of the AI perception system in unreal and used that data in a behavior tree for the AI and just used a navigation mesh for its ability to travel. 
Also unreal has a nifty feature on its character classes called RVO avoidance which seems to work well as crowd control for the AI. 

# Animation In Unreal
Animation was a very interesting obstacle as it contained many new topics of research for me such as anim notifies, state machines and animation blends in blueprint. 
Scripted a base montage class in c++ so i could use multiple child montages and utilized the same functionality to save time.
'void UMyAnimInstance::TargetAttack(int attackType)
{
	GLog->Log("//TODO attack moves");
	if (TargetAttackMontage)
	{
		FName CurrentSection = Montage_GetCurrentSection(TargetAttackMontage);

		//Determine which section is currently playing and jump to the next section
		//is possible
		if (CurrentSection.IsNone())
		{
			Montage_Play(TargetAttackMontage);
			if (attackType == 1)
				Montage_JumpToSection(FName("FirstAttackK"), TargetAttackMontage);
		}
		else if (CurrentSection.IsEqual("FirstAttackF") && bAcceptsSecondAttackInputF)
		{
			if (attackType == 0)
				Montage_JumpToSection(FName("SecondAttackF"), TargetAttackMontage);
		}
		else if (CurrentSection.IsEqual("SecondAttackF") && bAcceptsThirdAttackInputF)
		{
			if (attackType == 0)
				Montage_JumpToSection(FName("ThirdAttackF"), TargetAttackMontage);
		}
		else if (CurrentSection.IsEqual("ThirdAttackF") && bAcceptsFirstAttackInputK)
		{
			if (attackType == 1)
				Montage_JumpToSection(FName("FirstAttackK"), TargetAttackMontage);
		}
		else if (CurrentSection.IsEqual("FirstAttackK") && bAcceptsSecondAttackInputK)
		{
			if (attackType == 1)
				Montage_JumpToSection(FName("SecondAttackK"), TargetAttackMontage);
		}
		else if (CurrentSection.IsEqual("SecondAttackK") && bAcceptsThirdAttackInputK)
		{
			if (attackType == 1)
				Montage_JumpToSection(FName("ThirdAttackK"), TargetAttackMontage);
		}
	}
}'
This snippet plays the animation in different sections depending on the its current active section and its action(punch or kick).
Implementation of a state machine and performing blends and montages where all very new to me but definitely gave me some insight on the hurdles an animator in the 3d space is presented.
Speaking of animation actually animating the character was way more difficult than i first fought implementing not only key frames but different types of curves to control the flow of the animation.
![Animation montage for combat](/assets/images/Adminifight-CombatMontage.gif)

# Closing Thoughts
It started with me and my friends discussing what attacks we would have in a fighting game and how it would make a really good game. Well i thought that too and wanted to try making it. However i was pretty naive back then as I only just started university in computer science and had no experience making games. 
So it took a fair few years and even based my dissertation on learning the skills (See [2.5D Fighting game](https://kayway.github.io/game/2.5D-Fighting-Game/)).
However I soon learned that even though i could script and use most of the tools unreal had to offer, that is only the base of the project.
Mostly things on the artistic side but some slightly more technical roles such as implementing animations and blending.
This did not deter me and I actually learnt a lot about blender like Sculpting, 3D animation and modeling, UV unwrapping etc.

I will be adding these blender projects soon. I was debating adding them due to its relevance to Game programming.
{: .notice}
Sadly I had to stop production due to it taking way too much time, but i still often look back wanting to finish it which I may do someday. 
However this has given me a lot of experience in Game development and all of the necessary components and skills required and has also taught me a lot about the unreal engine.