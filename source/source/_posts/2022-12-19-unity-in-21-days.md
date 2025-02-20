---
title: Unity in 21 days
tags:
  - begginer
  - dotnet
  - unity
categories:
  - blog
disqusId: unity-in-21-days
date: 2022-12-19 11:17:32
---


{% asset_img pixelart.jpg Pixel Art %}

The title might be a bit misleading. If you assumed this post would be yet another Unity tutorial, it is not quite the case. Teaching you complex framework, such as Unity, in just about three weeks, is something I could never promise. It is more of a story of how I rewrote my high school project, written in Turbo Pascal, without having any real experience with Unity. Because, why not. And I have been postponing learning a thing or two about Unity for quite some time.

<!-- more -->

_This post is part of [C# Advent Calendar 2022](https://www.csadvent.christmas/). Cheers to [C# Advent](https://twitter.com/CsAdvent) for letting me participate!_

## Introduction

It has been so long since high school that, once I decided to go down this path, I had no chance to retrieve original game I developed, including source code. All I had was a fond memory of a game called Hungry Square, and a vague image of how the game looked like. It was as simple as it gets. You are a square on a two-dimensional plane, that can move in any of the four directions. Food is spawned randomly around game area. Once the square eats it, game would speed up, and an enemy would pop up and start bouncing around the screen. The objective of Hungry Square is to eat as much food as you can, without being hit by an enemy or colliding with the edge of the screen. Quite simple, right? It is the exact reason why I chose this project to fiddle with Unity.

If you want to skip the story, and jump straight into the game, you can do so [here](https://red-plant-049cdf903.2.azurestaticapps.net/).

## Day 1

There is always one question, when trying to learn a topic that you know nothing about: where to even start? Good tutorial goes a long way, and this has been a mantra that I have been using successfully through the years. Just finding one and flipping through it should help get our feet on the ground, and build from there on. In Unity's case, I have found a nice [four-parter on Sentry.io blog](https://blog.sentry.io/2022/03/21/unity-tutorial-what-you-need-to-know-before-developing-your-first-unity-game), going from basics of installing necessary tools, all the way to publishing finished game to Google Play Store. Lets get to reading!

## Day 2

Took me two days to read through all four posts and, although I haven't tried a single part of tutorial, I already feel more confident with whole endeavor. As I got familiar with Unity capabilities, a project manager in me already wants to establish some goals. I must remaster original game, and enhance it as much as possible. Since original offered pretty bare-bones experience, some animations and sounds would round it up nicely. As with almost everything I do these days, remaster should be available to play from web browser. I should try to add an Android version as well.

## Day 3

Finally installed and ran [Unity Hub and Editor](https://unity.com/downloads). I ignore available samples and create an empty project, using 2D template. It takes approximately seven minutes to create a new project, and another three just to build it for a WebGL platform. Hopefully further development will not take that much time. For a project without a single asset or line of code, build produced a 2.5 Mb large WebAssembly bundle. Not great, not terrible.

## Day 4

I start by trying to create a simple scene with a bordered background. After spending couple of hours, my initial confidence subsides. Once I started using Unity Editor, I was quickly overwhelmed with number of concepts, windows, and options. Maybe this project will not be a matter of just couple of days, as I initially thought.

## Day 5

Still trying to add background, but there is almost no progress. Looks like I'll have to reconsider learning on the fly, and actually dive more deeper into basic  Unity concepts. That said, quick search through [Unity's learn portal](https://learn.unity.com) reveals [Ruby's Adventure](https://learn.unity.com/project/ruby-s-2d-rpg), a tutorial on building 2D game. Decided to give it a try, as it covers all the parts I will probably need.

First chapter explains basic parts of Unity Editor. I learned what all windows are used for, and how to switch between scene and play mode. Next one covers adding game objects and manipulating camera. Managed to add player object (Ruby) and define it's health.

_Hint: if you are not seeing camera bounds in scene, select camera object and assign MainCamera tag to it._

## Day 6

Learning more about keyboard input and how to use it to move game objects. I found out there is old and new input system, but cannot tell what the difference is. Decided to stick with the old one, as it is used in Ruby's Adventure.

Next up is drawing background tiles and creating world map. Quite intuitive and quite easy to do, assuming you have the right assets. One thing I noticed is that this part of tutorial is pretty outdated, compared to current Unity version. With tutorial being barely two years old, things seem to be moving at a rapid pace in Unity world!

## Day 7

Following couple of chapters dealt with game object visibility and physics system. I learned how to order overlapping objects and what a pivot is. I am not sure I will use this knowledge when working on Hungry Square, as there is almost no overlapping between game elements. However, I also learned how to create prefabs, which are used as templates for objects in scene. When thinking about enemies and food, which are added to scene dynamically, prefabs seem like a natural way how to implement them.

When it comes to physics, Unity's system revolves around rigid body and collider components, which are used to model and detect game object interactions. Not sure I will ever utilize these components.

_Note from Day 11: rigid body and collider are essential components for Hungry Square remaster. They are extensively used to detect interactions between player, food and enemies, as well as model enemy movement in game world. And I thought I would never use them..._

## Day 8

Next tutorial chapter taught me how to add moving enemies to the scene, and how to detect when a player collides with them. Learned how to create animations, using sprites, controllers and state machines. Animated movement of both player and enemies, for extra polish. After all, no one likes static objects. As a next step, I added projectiles, so the player can defend against enemies. Learned about collision layers, which made sure projectiles collide only with enemies.

One small thing that tutorial also went through is moving from a static camera, to one that will follows player. As I plan to have a single screen as playable area, I might not need this type of camera. Will keep it in reserve anyways.

Following part taught me how to make the game even more visually appealing, by using particles. Utilized them to create smoke effect on enemies. I might need them if I decide to go for extra polish for my remaster. One example would be adding particle effects on food and enemy spawn.

## Day 9

Nearing the end of tutorial. This chapter showed me how to create scene overlay, which will be used to show player health bar. It is rather complicated to accomplish, event with detailed instructions and assets available out of the box. In the end, I might not even need, as Hungry Square ends as soon as the player is hit by an enemy.

Next part is about creating non-playable characters and interacting with them. It also shows how to create dialog and quests. As none of these features are planned for Hungry Square, I decided to skip this tutorial altogether. Might come back to it eventually, for completeness sake.

## Day 10

Last few tutorials dealt with quite important concepts. First one taught me how to add audio listeners, and how to produce 2D and 3D sounds. If I find suitable audio clips, I might add them to objects in my game.

Finally, I learned how to build the whole project into an executable. Realized that I had wrong platform selected the entire time, so I switched to WebGL. Analyzed and tried to learn about all available options, as WebGL will be my primary platform.

## Day 11

With all tutorials completed, it is time to finally start working on Hungry Square remaster! With all important Unity concepts still fresh in my head, I manage to get a lot of work done on first sitting. Immediately found some free sprite assets in the shape of a square, and used them to create prefabs for player, enemies and food. Instead of handling enemy movement on my own, like in the original game, I try to utilize Unity physics system. All I had to was create an edge collider around game area, assign absolutely elastic material to it, and do the same for each enemy. On enemy spawn, an impulse force was applied to its rigid body, and physics system took care of the rest. Easy peasy!

To detect player collisions, I have added colliders to player, enemies and food, and assigned proper tags to each. Had to prevent enemies from crashing into food, so moved each one to dedicated collision layer, and turned off interactions between them.

_Issue detected: enemies are configured to speed up after each edge bounce, before I introduce concept of game speed. Noticed that after some time, enemies get so fast, that they skip screen edge collider, and simply disappear out of the game. Funny thing to occur, sine the original game had the exact same problem, due to enemy position after bounce being miscalculated sometimes. Something I must not forget to fix later._

## Day 12

This time I focused on adding game logic. Handled player collision with enemy, screen edge and food. Soon I will have to introduce concept of scoring, as well as create game over screen to show in the end.

After eating food, game spawns another enemy and food at random location. It works, but I am not happy with implementation. Player and game objects reference all prefabs, in order to dynamically create them or invoke their methods. Will try to look for a better solution, and reduce coupling between objects.

Spent some time investigating disappearing enemy issue. It seems that this is a known limitation of Unity's physics engine, which checks collisions only at fixed points in time. Fixing such behavior can only be achieved by limiting maximum object speed. Enemies should not be allowed to go through screen edge in one fixed update, so I will set maximum speed to enemy length divided by period of fixed update. Seemed to help, as I don't detect any enemies dropping out of the game area anymore.

## Day 13

Found a way to implement prefab registry, using dedicated singleton object. Reworked all types to use prefab registry, instead of assigning them as properties. I am sure there is an even better way to reference prefabs from game objects, but hey, I am not a professional Unity developer :smile:

## Day 14

Today I mostly dealt with endgame features. Went through a tutorial on how to implement game over screen, and added it to Hungry Square. It will be shown on collision with either enemy or screen edge. After finishing the game, it is possible to restart it via menu button. 

_Issue detected: After restarting game, I noticed that keyboard input is not detected anymore, even though player and initial food objects are spawned correctly. Might be just a minor hiccup because of whole scene reload, but surely something I need to address soon._

## Day 15

Used Game Controller to centralize game speed, instead of spreading it through various scripts. Player and enemy movement will be adjusted based on this value, which is increasing by couple of percent each time a player consumes food. Works well, and I remove enemy speedup on screen edge bounce.

Input is still not working after game restart, event though I tried couple of fixes. Will have to check if it happens in built game as well, but I am starting to get worried.

## Day 16

Since I wanted to test built version anyways, I dedicated all available time today to get CI/CD pipeline up and running. Had a lot of fun discovering how Unity games are built and tested, and how to deploy build output. So much fun actually, that I documented the whole process [in a companion article](../github-workflow-unity-azure-static-webapps).

## Day 17

Finally managed to solve non-responsive inputs after game is restarted. Turns out inputs were working fine the whole time, but the game remained paused after scene reload. Adjusted time scale after scene reload, and the game runs as expected. Crisis averted!

Continuing to work on improving overall player experience. Food and enemies were spawning randomly, which sometimes caused immediate collision with player. Adjusted this behavior to a more predictable pattern, and made them spawn randomly on the opposite space of playable area. Also started tracking score and using game over screen to show it to the player.

## Day 18

Encountered an issue on game over screen, happening for built game only. An exception was being thrown, but it was impossible to find out what part of code was causing it. Had to learn how to produce a debug build and expose exception call stack. Turns out an instance of a text field was not properly assigned to game over screen, which caused a NullReferenceException. However, this issue does not arise in play mode. Strange... Solved it by reassigning text field instance to game over object.

## Day 19

Worked on quality of life improvements. Player, food and enemy collision areas were too big, so I decided to reduce them to fit actual shape of each object. By increasing main sprite pixels per unit a bit, made all relevant objects smaller, and made the game area look a bit bigger. I guess I could have achieved similar behavior by moving the camera a bit further from the player.

## Day 20

Added main menu and included a small game description. Initially I wanted to implement it in the same way as game over screen, but soon realized that it is better to extract it to a separate scene. Main menu scene will be shown first and, once start button is pressed, game scene will be loaded.

## Day 21

Putting in last few tweaks and doing a final round of testing. Everything seems to be in order, and Hungry Square remastered is ready to be shipped!

## Final thoughts

It has been quite a ride. My initial assumption, that I would rework the game in couple of days, turned into endeavor that lasted almost the whole month. Nevertheless, I am quite happy with the end product, especially because I managed to combine it with other thing I love - Azure!

On the other hand, I am not so satisfied that remaster is still pretty bare-bones. Visually nothing much changed from a 20 year old game, and the lack of animations and sounds makes the game look very rudimentary. Surely something I will seek to correct in the future. But for now, I think I have a pretty good base to build upon.

## Conclusion

If you ever think about going the same path as me, I hope this post would help you avoid making mistakes that I did. Although Unity is complex, learning about it's various concepts can be quite fun. It is used to make games, after all.

Coming back to Hungry Square, the game is up and running in an Azure Static Web Apps instance, and it will remain deployed there for the time being. You can see the final result, and try it out [via an URL that was randomly assigned by Azure Static Web Apps](https://red-plant-049cdf903.2.azurestaticapps.net/) (sorry for any anti-virus alerts I caused). My high score is 24, can you try to beat it? Let me know!
