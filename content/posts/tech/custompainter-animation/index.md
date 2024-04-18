---
title: CustomPainter Animations in Flutter
summary: CustomPainter is an amazing way to have total control on what you want to paint on screen. Animating these could be tricky but the results are worth it.
date: 2024-04-20T00:00:00+05:30
tags: ["Flutter", "Dart", "UI", "CustomPainter", "Animation"]
series: ["Tech", "Flutter"]
cover:
  image: "images/cover.png"
  alt: "CustomPainter Animations in Flutter cover photo"
  relative: true
---

Hey everyone! 👋

Sorry for being lazy but I'm back (at least for now 😉).

Ok back to Flutter and Animations, two of my favourite things in frontend development.

{{< rawhtml >}}

<center>
  <video width="50%" autoplay loop>
    <source src="./videos/animation-full.mp4" type="video/mp4">
    Full
  </video>
</center>

{{< /rawhtml >}}

Last year when I was still interning at a company I was asked to build the above animation. It was an interesting but a bit difficult task as the animation had a lot of moving parts and the data inside (amount) will change, hence I cannot use a video or something like that and video would take more space than code. Let me take you through my thought process of building this.

Whenever I think about building an animation I look at this amazing chart by the Flutter team.

[![Which is better approach for an animation in Flutter](https://docs.flutter.dev/assets/images/docs/ui/animations/animation-decision-tree.png)](https://docs.flutter.dev/ui/animations)

By looking at this chart you can see that our animation is basically lines and circles. So it looks like a drawing and I thought it's easy to code it (boy I was so wrong 🥲). Hence I picked CustomPainter.

Before I go ahead I would like to mention that you can also create a [RenderObject](https://api.flutter.dev/flutter/rendering/RenderObject-class.html) in Flutter if you want any of the element to be clickable or have elements which are complex to build with CustomPainter but easier using existing widgets.

Moving on to the how I built this animation -

1. Divide the animation is parts.
1. Build the static components without any animations.
1. Animate those components.
1. Optimize the animation.

### Dividing an animation

It's better to divide a longer an animation into segments, it will make it a lot easier for you to code it and more importantly understand it.

You can divide when the direction of motion, color, or focus (Object which is animating) of animation changes.

> Trust your gut feeling.

When I saw the end state of animation I divided it in 3 sections to focus on.

{{< figure src="./images/animation-end.jpg" width="50%" align="center" >}}

1. Red - Main circle
1. Blue - Body lines
1. Yellow - Children circles

1. Tracking Main circle's motion

{{< rawhtml >}}

<center>
  <video width="50%" autoplay loop>
    <source src="./videos/animation-1.mp4" type="video/mp4">
    Full
  </video>
</center>

{{< /rawhtml >}}

My first task would move the main circle up. It's pretty easy as I just need to move the center of the circle and you have your animation.
