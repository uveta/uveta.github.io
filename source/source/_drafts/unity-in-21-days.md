---
title: Unity in 21 days
tags:
  - begginer
  - dotnet
  - unity
categories:
  - blog
disqusId: unity-in-21-days
---

{% asset_img game.jpg Unity game %}

Story about how I converted my high school project, written in Pascal, to Unity. Because, why not. And I have been postponing learning a thing or two about Unity for quite some time.

<!-- more -->

_This post is part of [C# Advent Calendar 2022](https://www.csadvent.christmas/). Cheers to [Matthew D. Groves](https://twitter.com/mgroves) for letting me participate!_

## Introduction

describe what is the game about

## Day 1

There is always one question, when trying to learn a topic that you know nothing about: where to even start? Good tutorial goes a long way, and this has been a mantra that I have used successfully through the years. Just finding one and browsing it should help put your feed firm on the ground, and build from there on. In Unity's case, I have found a nice [four-parter on Sentry.io blog](https://blog.sentry.io/2022/03/21/unity-tutorial-what-you-need-to-know-before-developing-your-first-unity-game), going from basics of installing necessary software, all the way to publishing finished game to Google Play Store. Lets get to reading!

## Day 2

Took me two days to read through all four posts and, although I haven't tried a single part of tutorial, I already feel more confident with whole endeavor. Getting familiar with Unity capabilities, project manager in me already wants to establish some goals. I must remaster original game, and enhance it as much as possible. Since original offered pretty bare-bones experience, some animations and sounds would round it up nicely. As with almost everything I do these days, remaster should be available to play from web browser. Should try to create Google Play version as well.

## Day 3

Finally installed and ran [Unity Hub and Editor](https://unity.com/downloads). I ignore available samples and create an empty project, using 2D template. It takes approximately seven minutes to create a new project, and another three just to build it. Hopefully further development will not take that much time. As I switched to using WebGL, build output is an 2.5 Mb WebAssembly, for a project without a single asset or line of code. Not great, not terrible.

## Day 4

Want to create a simple scene with a bordered background. After trying for couple of hours, my initial confidence subsides. Once I started using Unity Editor, I was quickly overwhelmed with number of concepts, windows, and options. Maybe this project will not be a matter of just couple of days, as I initially thought.

## Day 5

Still trying to add background, but there is almost no progress. Looks like I'll have to reconsider learning on the fly, and actually dive more deeper into basic concepts Unity has to offer. That said, quick search through Unity's learn portal reveals [Ruby's Adventure](https://learn.unity.com/project/ruby-s-2d-rpg), a tutorial on building 2D game. Decided to give it a try, as it covers all the parts I will probably need.

First chapter explains basic parts of Unity Editor. I learned what all windows are used for, and how to switch between scene and play mode. Next one covers adding game objects and manipulating camera. Managed to add player object (Ruby) and define it's health.

_Hint: if you are not seeing camera bounds in scene, select camera object and assign MainCamera tag to it._

## Day 6

Learning more about keyboard input and how to use it to move game objects. I found out there is old and new input system, but cannot tell what the difference is. Decided to stick with the old one, as it is used in Ruby's Adventure.

Next up is drawing background tiles and creating world map. Quite intuitive and quite easy to do, assuming you have the right assets. One thing I noticed is that this part of tutorial is pretty outdated, compared to current Unity version. Since tutorial is barely two years old, things seem to be moving at a rapid space in Unity world!

## Day 7

Following couple of chapters dealt with game object visibility and physics system. I learned how to order overlapping objects and what a pivot is. I am not sure I will use this knowledge when working on Hungry Square, as there is almost no overlapping between game elements. However, I also learned how to create prefabs, which are used as templates for objects in scene. When thinking about enemies and food, which are added to scene dynamically, prefabs seem like a natural way how to implement them.

When it comes to physics, Unity's system revolves around rigid body and collider components, which are used to model and detect game object interactions. Not sure I will ever utilize these components.

_Note from Day 11: rigid body and collider are essential components for Hungry Square remaster. They are extensively used to detect interactions between player, food and enemies, as well as model enemy movement in game world. And I thought I would never use them..._

## Day 8

Next tutorial chapter taught me how to add moving enemies to the scene, and how to detect when a player collides with them. Learned how to create animations, using sprites, controllers and state machines. Animated movement of both player and enemies, for extra polish. After all, no one likes static objects. As a next step, I added projectiles, so the player can defend against enemies. Learned about collision layers, to allow shot projectiles to collide only with enemies.

One small thing that tutorial also went through is moving from a static camera, to one that will follows player. As I plan to have a single screen as playable area, I might not need this type of camera. Will keep it in reserve anyways.

Following part taught me how to make the game even more visually appealing, by using particles. Utilized them to create smoke effect on enemies. I might need them if I decide to go for extra polish for my remaster. One example would be adding particle effects on food and enemy spawn.

## Day 9

Nearing the end of tutorial. Following chapter showed me how to create scene overlay, which will be used to show player health bar. It is rather complicated to accomplish, event with detailed instructions and assets available out of the box. In the end, I might not even need, as Hungry Square ends as soon as the player is hit by an enemy.

Next part is about creating non-playable characters and interacting with them. It also shows how to create dialog and quests. As none of these features are planned for Hungry Square, I decided to skip this tutorial altogether. Might come back to it eventually, for completeness sake.

## Day 10

Last few tutorials dealt with quite important concepts. First one taught me how to add audio listeners, and how to produce 2D and 3D sounds. If I find suitable audio clips, I might add them to objects in my game.

Finally, I learned how to build the whole project into an executable. Realized that I had wrong platform selected the entire time, so I switched to WebGL. Analyzed and tried to learn about all available options, as WebGL will be my primary platform.

## Day 11

With all tutorials completed, it is time to start work on Hungry Square project.

Day 11
Finally started working on hungry square. Added prefabs for player and enemies, based on simple sprites. Managed to add colliders to the edge of the screen dynamically. Added collider to enemy. Used absolute bouncy material. Using Unity physics engine, make enemies bounce and speedup, instead of programming it manually.
Enemies missing screen collider, due to high velocity. Limit velocity to max width of enemy collider. Try to make screen collider thicker?

Day 12
Enemy fell out of screen on one of test runs, have to find a solution for that.
Created food and had trouble detecting collision with player. Player can pic up food and collide with enemies. Have to come up with score counter and game over screen.
Creating enemies and foor randomly, but I am not satisfied with way it was implemented. Have to find a better way to dynamically load prefabs, instead of assigning them via script fields.

Day 13
Found a way to implement prefab registry, using dedicated singleton. Reworked all types to use it,instead of instances assigned from inspector.

Day 14
Added Game Over screen, shown after collision with enemy or border. Added game restart button. After restarting the game, keyboard input stops working, have to investigate why.

Day 15
Moved game speed to Game Controller. Adjusting player and enemy speed based on it. Increasing game speed after each food pickup. Input still not working after restart, have to check if it happens in built game as well.

Day 16
Setup GitHub workflow to build and deploy game to Azure Static Web App. Several issues: several binary files missing from LFS, had to force inckude them and rewrite main branch history; files modified after checkout, could not solve it, had to allow dirty builds; obsolete scene caused build to fail, make sure all required scenes are configured in File->Build Settings->Scenes In Build; configure app_location action parameter to point to output with index.html. Will include details in README.md.
Input does not work after restart in built game as well.

Day 17