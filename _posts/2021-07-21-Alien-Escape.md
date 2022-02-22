---
title: "Alien Escape"
classes: wide
header:
  video:
    id: cL_p2HpXDeo
    provider: youtube
author_profile: false
sidebar:
  - title: "Genre"
    image: "/assets/images/AlienEscape-IntroScene.png"
    text: "3D, Top-Down, Twin Stick, Shooter, Bullet Hell"
  - title: "Platform"
    text: "Windows x64"
categories:
  - Game
tags:
  - Unreal Engine
  - C++
---
<iframe frameborder="0" src="https://itch.io/embed/1109423?bg_color=479b38&amp;fg_color=8c1090&amp;link_color=5bfaf7&amp;border_color=333333" width="552" height="167"><a href="https://kayofways.itch.io/alien-escape">Alien Escape by KayOfWays</a></iframe>

# Overview
[Source Code](https://github.com/kayway/AlienEscape)

This is a twin stick bullet hell game. You are a alien ship that crash landed on this planet but got captured by the governing authorities and researched your technology to develop their own military strength.
This was my attempt at a bullet hell game jam so think of it as a prototype as im working on a fully fleshed out game.

# Gameplay
- Use Abilities like Shield and Super speed to protect yourself and redirect lasers
- Enemy AI will hunt you down and attack from a safe distance, take out the gateways to stop them spawning 
- Towers absorb all fire turning the energy to rapid fire lasers or even a mega laser bomb
- Watch your health and energy as they can go down quick, always keep it up by going over the coloured pads

# AI
![Behavior Tree](/assets/images/AlienEscape-BT.png)
This was my first experience with EQS(Environmental Query System) which is a very useful system which allows the ai to determine the best path using an array of nodes against conditions.
I designed this system to keep the AI a safe distance while keeping the player in its sight.
I had to limit the distance a bit to allow the player to destroy them easier. 

![EQS Diagram](/assets/images/AlienEscape-EQS.png){: .align-left}
There was no need for a perception system for the AI as i wanted the units to always know where the player was.
However I did perform a frequent line trace to ensure the AI had the player in sight before it fires(see TargetLOS in the behavior tree image above).
Below is the Function to determine line of site.
```c++
bool UTargetLOSDecorator::CalculateRawConditionValue(UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory) const
{
	bool HasLOS = true;
	if (AAIController* const Cont = Cast<AEnemyAIController>(OwnerComp.GetAIOwner()))
	{
		if (ABulletHellJamPawn* const BulletHellJamPawn = Cast<ABulletHellJamPawn>(Cont->GetPawn()))
		{	
			UObject* Target = OwnerComp.GetBlackboardComponent()->GetValueAsObject(TargetKey.SelectedKeyName);
			// Perform trace to retrieve hit info
			AActor* TargetActor = Cast<AActor>(Target);
			FCollisionQueryParams TraceParams(SCENE_QUERY_STAT(AILosTrace), true, OwnerComp.GetOwner());
			TraceParams.bReturnPhysicalMaterial = true;
			TArray<AActor*> FoundActors;
			UGameplayStatics::GetAllActorsOfClass(GetWorld(), ABulletHellJamProjectile::StaticClass(), FoundActors);
			const FVector StartLocation = BulletHellJamPawn->GetActorLocation();
			if (TargetActor)
			{
				const FVector EndLocation = TargetActor->GetActorLocation();
				FHitResult Hit(ForceInit);
				GetWorld()->LineTraceSingleByChannel(Hit, StartLocation, EndLocation, ECC_Visibility, TraceParams);
				
				if (Hit.bBlockingHit == true)
				{
					HasLOS = false;
					// We hit something. If we have an actor supplied, just check if the hit actor is an enemy. If it is consider that 'has LOS'
					AActor* HitActor = Hit.GetActor();
					if (Hit.GetActor() != NULL)
					{
						// If the hit is our target actor consider it LOS
						if (HitActor->ActorHasTag(FName("Player")))
						{
							HasLOS = true;
						}
					}
				}
			}
		}
	}
	return HasLOS;
}
```

# Final Thoughts
It was nice not having to worry about animation for this game. This definitely felt like a much more complete Game, but would definitely would like to work on it further.
Maybe a fully fleshed game would be fun to make, as i had many more ideas for this game with differing unit types and tower types to add some variety and interesting challenges.