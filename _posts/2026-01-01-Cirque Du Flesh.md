---
title: "Cirque Du Flesh"
classes: wide
header:
  teaser: /assets/images/cirque-du-flesh-banner.png
  video:
    id: g-BJVqXupLM
    provider: youtube

sidebar:
  - title: "Genre"
    image: /assets/images/cirque-du-flesh-banner.png
    text: 3D, Horror, First person
  - title: Platform
    text: Windows x64
  - title: Technologies
    text: C++, Unreal Engine, Perforce
  - title: Skills
    text: Tool Development
categories:
  - Personal Projects
tags:
  - Unreal Engine
  - C++
  - Blueprint
  - UI Development
  - AI Development
---

A carnival horror game where you need to play carnival games to get tickets and feed the flesh walls to progress.

<iframe frameborder="0" src="https://itch.io/embed/4139359?linkback=true&amp;bg_color=000000&amp;fg_color=eeeeee&amp;link_color=fa5c5c&amp;border_color=333333" width="552" height="167"><a href="https://kayofways.itch.io/cirque-du-flesh">Cirque Du Flesh by KayOfWays, Rheba, Tarith</a></iframe>

[Source Code](https://github.com/kayway/Cirque-Du-Flesh)

# Key Contributions
- Designed and implemented core gameplay systems, including a custom character controller, AI, player interaction, inventory, dialogue, and save/load.
- Developed and integrated gameplay features including UI, carnival games and item pickups.
- Integrated multidisciplinary assets (audio, UI, 2D art) into Unreal Engine, ensuring cohesive gameplay functionality.

# AI System
Created a AI system for enemy AI, with chase and combat mechanics, using Unreal behaviour trees. 
Its setup in a way that once the player has been spotted they will always pursue the player.
To do this I implemented the following:
- Custom decorator for float-based decision thresholds.
- Behavior Tree service to update player state (location, line of sight, distance).
- Custom BTTasks for combat actions, including attack handling and stagger reactions.

![Behavior Tree](/assets/images/Adminifight-Behaviortree.png)
This tree manages 4 states:
- Idle: No movement.
- Stagger: prevent combat, fall back. 
- Combat: allow attacks with delays;
- Dead: prevent any actions.

## Stagger BTTask
![Enemy Stagger](/assets/images/CirqueDuFlesh-Stagger.gif)
A custom BTTask was implemented to handle stagger responses and temporary interruption of AI behaviour.
When triggered, the task applies a directional pushback to the enemy and enters a timed state where actions are disabled. The task only completes once the timer elapses, ensuring the AI remains in the stagger state for the full duration.

The same timer is also used to drive visual feedback by gradually fading the sprite’s stagger colour, linking gameplay state with presentation.
![Blueprint](/assets/images/CirqueDuFlesh-StaggerBTTask.png)


# Player Controller
Implemented a first-person player controller handling interaction, input state, and sprite state management.

- Interaction system for detecting and triggering gameplay elements
- Input and menu state handling
- Sprite state management for visual feedback
- Gun shooting and melee combat

## Interaction System
Designed a flexible interaction system to detect and interact with objects in front of the player. The system performs a forward sphere trace each frame to identify valid interactables within range.
To support extensibility, interactions are handled via an interface, allowing both actors and components to define custom interaction behaviour without tight coupling to the player controller.
When a valid intractable is detected, it is cached as the current target. During dialogue interactions, updates are paused to prevent conflicting input and ensure consistent state handling.

```c++
void APlayerCharacter::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);	

	if (InConversation)
	{
		return;
	}

	UWorld* World = GetWorld();
	if (!World)
	{
		return;
	}

	float Radius = 50.f;
	FCollisionShape Sphere = FCollisionShape::MakeSphere(Radius);
	FCollisionQueryParams Params;
	Params.AddIgnoredActor(this);

	FVector Start = GetLOSStartPosition();
	FVector End = GetLOSEndPosition(InteractionRadius);
	TArray<FHitResult> Hits;

	bool bHit = World->SweepMultiByChannel(
		Hits,
		Start,
		End,
		FQuat::Identity,
		ECC_Visibility,
		Sphere,
		Params
	);

	InteractionTarget = nullptr;

	FVector ImpactPoint = FVector::Zero();
	if (bHit)
	{
		for (FHitResult Hit : Hits)
		{
			AActor* HitActor = Hit.GetActor();
			if (HitActor->Implements<UInteractionInterface>())
			{
				if (IInteractionInterface::Execute_CanInteract(HitActor))
				{
					ImpactPoint = Hit.ImpactPoint;
					InteractionTarget = HitActor;
					break;
				}
			}
			else
			{
				HitActor->GetComponentsByInterface(UInteractionInterface::StaticClass());
				for (UActorComponent* Component : HitActor->GetComponentsByInterface(UInteractionInterface::StaticClass()))
				{
					if (IInteractionInterface::Execute_CanInteract(Component))
					{
						ImpactPoint = Hit.ImpactPoint;
						InteractionTarget = Component;
						break;
					}
				}

				if (InteractionTarget)
				{
					break;
				}
			}
		}
	}

	if (DebugInteraction)
	{
		// Debug draw
		FColor Color = InteractionTarget ? FColor::Red : FColor::Green;

		DrawDebugLine(World, Start, End, Color, false, 1.f, 0, 1.f);
		DrawDebugSphere(World, InteractionTarget ? ImpactPoint : End, Radius, 16, Color, false);
	}
}
```

# Save System
Implemented a lightweight save system using Unreal’s SaveGame framework to persist player state across sessions.
Game data such as player progress and key variables are stored in a custom save object and managed through the Game Instance, providing a centralized access point for saving and loading.
The system is triggered when speaking to zoltan, ensuring important state is preserved without requiring constant writes.

![Zoltan](/assets/images/CirqueDuFlesh-Zoltan.gif)
# Dialogue System
Implemented a modular dialogue system using a custom component that can be attached to any actor. 
The system integrates with the interaction framework, allowing dialogue to be triggered consistently across different gameplay elements.

Dialogue is structured as a sequence of DialogueLine objects, each containing audio, subtitle text, and timing data. 
This enables data-driven dialogue playback with synchronised audio and subtitles.

## Dialogue Line Object

Dialogue playback is initialised by setting the audio source and triggering the first line in the sequence.

```c++
void UDialogueLine::PlayDialogue(UTextBlock* DialogueTextObject, UAudioComponent* DialogueSpeaker)
{
	UE_LOG(DialogueLine, Log, TEXT("Playing dialogue line %s..."), *GetName());
	if (!DialogueTextObject)
	{
		UE_LOG(DialogueLine, Error, TEXT("No text object for subtitles found!"));
		return;
	}

	if (!VoiceLine)
	{
		UE_LOG(DialogueLine, Error, TEXT("No valid voice line found!"));
		return;
	}

	if (!DialogueSpeaker)
	{
		UE_LOG(DialogueLine, Error, TEXT("No valid speaker found!"));
		return;
	}

	TextObject = DialogueTextObject;

	DialogueSpeaker->SetSound(VoiceLine);
	DialogueSpeaker->Play();
	PlayLine();
}
```

Dialogue lines are sequenced using a timer-based system, allowing each line to display for its defined duration without blocking gameplay.
```c++
void UDialogueLine::PlayLine()
{
	if(lineIdx >= Lines.Num())
	{
		UE_LOG(DialogueLine, Log, TEXT("Finished playing dialogue line %s."), *GetName());
		OnFinishedDialogue.Broadcast();
		return;
	}

	GetWorld()->GetTimerManager().SetTimer(
		LineTimerHandle,
		this,
		&UDialogueLine::PlayLine,
		Lines[lineIdx].Duration,
		false
	);

	TextObject->SetText(Lines[lineIdx].Line);

	lineIdx++;
}
```
# Inventory System
![Enemy Stagger](/assets/images/CirqueDuFlesh-Inventory.gif)

Implemented a data-driven inventory system using Unreal Engine’s UI framework, separating. Inventory items are represented as data objects, which are fed into a TileView to dynamically generate UI entries. 
This allows the interface to update automatically as the inventory changes, without tightly coupling UI logic to gameplay systems.
The system uses entry widgets to represent individual items, enabling consistent rendering and interaction handling for each inventory slot.

This approach supports scalability, making it easy to add new item types or modify the UI without restructuring the underlying system.
Decoupling inventory data from UI ensures the system remains flexible and maintainable, while leveraging Unreal’s list-based UI allows efficient rendering and updating of inventory elements.

# Final Thoughts
Had a lot more fun with this one as there was a lot of different gameplay systems and features. 
Also using unreal save system was a great learning experience on how to handle serialized save data.