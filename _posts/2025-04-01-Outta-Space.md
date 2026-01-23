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
  - title: Technologies
    text: C#, Unity
  - title: Skills
    text: Tool Development
categories:
  - Personal Projects
tags:
  - Unreal Engine
  - C++
  - Blueprint
  - UI Development
---

An game where you need to escape a expanding black hole. Dodge asteroids as you try to reach the end goal before the dark hole engulfs you.
I was responsible for all the asset implementation(Sound, 3D Assets, UI, etc.) and functionality.

<iframe height="175" width="560" src="https://itch.io/embed/3422717?linkback=true&amp;border_width=5&amp;bg_color=000000&amp;fg_color=ffffff&amp;link_color=ad1ab9&amp;border_color=ad1ab9" frameborder="0">&lt;a href=&quot;<a href="https://kayofways.itch.io/outta-space">Outta&quot; class=&quot;redactor-linkify-object&quot;&gt;https://kayofways.itch.io/outta-space&quot;&gt;Outta</a> Space by KayOfWays, Drnisme, Rheba&lt;/a&gt;</iframe>

[Source Code](https://github.com/kayway/OuttaSpace)

# Key Contributions
- Designed and programmed a custom Pawn movement controller in C++ for responsive, smooth character handling.
- implementeed a time-pressure system that gets closer to the player, accelerating over time (black hole).
- Implemented gameplay features like player state handling, item pickups health system etc.
- Implemented all the UI systems(Player HUD, Dialogue UI, Options Screen, etc.)
- Implemented Settings with save functionality.
- Perforce Server setup and maintenence.

# Player Controller
I created a custom Pawn controller in C++, with some funny rotation where the planet faces towards your input.
We always move forwards and we increase speed overtime, with movement limited to Up/Down Left/Right, with the camera bounds as its movement limits.

I realized that because physics is resolved by the movement component and the component sweeps only when it registers movement.
Therefor we need to call AddMovementInput to simulate physics, as we cant enable "Simulate Physics" option on the capsule component.

```c++
void APlanetController::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);
	
	if (IsDead)
	{
		return;
	}

	//We add a bit of movement input to resolve  physics as pawns cannot have simulate physics enabled.
	AddMovementInput(FVector::ForwardVector, 0.01f, true);
	
	if (HasTeleported)
	{
		auto Location = GetActorLocation();
		SpawnLocation = FVector2D(Location.Y, Location.Z);
		MovementComponent->Velocity = FVector::ZeroVector;
	}
	auto CurrentLocation = GetActorLocation();
	
	auto Max = SpawnLocation + MaxExtents;
	auto Min = SpawnLocation - MaxExtents;
	auto ClampedY = FMath::Clamp(CurrentLocation.Y, Min.X, Max.X);
	auto ClampedZ = FMath::Clamp(CurrentLocation.Z, Min.Y, Max.Y);

	ForwardSpeed = FMath::Clamp(ForwardSpeed + ForwardAcceleration * DeltaTime,
		InitialSpeed,
		ForwardMaxSpeed);

	SetActorLocation(FVector(CurrentLocation.X + ForwardSpeed, ClampedY, ClampedZ));

	auto Velocity = MovementComponent->Velocity;
	auto NewRotation = Velocity.ToOrientationRotator();

	NewRotation.Yaw = GetActorRotation().Yaw + SpinSpeed * DeltaTime;

	SetActorRotation(NewRotation);
}
```

I needed to have the camera seperate from the player so it coan move frealy with it attempting to follow them. We only want to follow the planet on one axis but we can retain the rest.

```c++
void APlayerCamera::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

	if (TargetPawn == nullptr)
	{
		return;
	}

	auto ActorLocation = GetActorLocation();
	auto PlanetControllerLocation = TargetPawn->GetActorLocation();
	auto TargetLocation = FVector(PlanetControllerLocation.X, ActorLocation.Y, ActorLocation.Z);
	SetActorLocation(TargetLocation);
}
```
# Black Hole System
![Black Hole BP](/assets/images/OuttaSpace-BlackHole.png)
Implemented a time-pressure system that gets closer to the player, accelerating over time. If it touches the player the game is over. 
It can be temporarilly slowed by picking up the hourglasses.

The UI bar at the bottom tracks the black hole, player and the end goal.
![Progress Tracker](OuttaSpace-ProgressTracker.png)

# Final Thoughts
This was a fun jam to work on, and where i learn t the most abaut using perforce with unreal and sharing the project with non-programmer collaberators.