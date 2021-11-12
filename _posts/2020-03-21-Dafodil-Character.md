---
title: "Dafodil-Character"
classes: wide
categories:
  - Assets
tags:
  - Blender
  - Unreal
galleryDafodil:
  - image_path: /assets/images/Dafodil/Dafodil-Front.png
    alt: "front"
  - image_path: /assets/images/Dafodil/Dafodil-Side.png
    alt: "side"
  - image_path: /assets/images/Dafodil/Dafodil-Back.png
    alt: "back"
galleryShoafy:
  - image_path: /assets/images/Dafodil/Shoafy-Front.png
    alt: "front"
  - image_path: /assets/images/Dafodil/Shoafy-Side.png
    alt: "side"
  - image_path: /assets/images/Dafodil/Shoafy-Back.png
    alt: "back"
---

# Overview

{% include gallery id="galleryDafodil" caption="Dafodil Character." %}

This was a character I made for "AdminiFight" game I was developing. It is fully rigged and ready to be used in unreal. I also did a sheep model which is also rigged and animated

{% include gallery id="galleryShoafy" caption="Sheep Character." %}
# Rigging and animation
![Character combat animation.](/assets/images/Dafodil/Dafodil-Combat.gif){: .align-center}
For the rigging I used Rigify a very handy blender addon which provides an extensive skeleton with inverse kinematic bones already implemented.
Animations was done in blender and imported to unreal, then used some blend spaces and state machine to create a functioning character.
![Sheep idle to run animation.](/assets/images/Dafodil/Shoafy-IdleRun.gif){: .align-center}
Doing an animal definitely changed my thought process, especially the way an animal walks and run.
# Unreal Compatibility
For Compatibility with unreal and to keep the skeleton functional in unreal, io had to use Uefy a very handy addon which can take complex rigs and formats them to unreal engine skeletal spec, making it compatible with epic skeletons and removes unnecessary bones when imported into unreal.
# Final Thoughts
I didn't go into much detail about the modelling, retopology etc, because I wanted to focus on the animation and the exporting into unreal as this was what became the biggest obstacle to me. I can see some animations need some work but for my fist animated character I fell I did well and I have learnt a lot.
I would like to point out that even though my career focus may not be relevant to this I just thought it would be nice to have these on here as fun side projects and as a small badge of pride as I never saw myself learning Blender at the beginning