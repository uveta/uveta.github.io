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

With all tutorials completed, it is time to finally start working on Hungry Square remaster! With all important Unity concepts still fresh in my head, I manage to get a lot of work done, on first sitting. Immediately found some free sprite assets in the shape of a square, and use them to create prefabs for player, enemies and food. Instead of handling enemy movement on my own, like in the original game, I try to utilize Unity physics system. All I had to was create an edge collider around game area, and assign absolutely elastic material to it and to each enemy. On enemy spawn, an impulse force was applied to its rigid body, and physics system took care of the rest. Easy peasy!

To detect player collisions, I have added colliders to player, enemies and food, and assigned proper tags to each. Had to prevent enemies from crashing into food, so moving each one to dedicated collision layer, and turning off interactions between them.

_Issue detected: enemies are configured to speed up after each edge bounce, before I introduce concept of game speed. Noticed that after some time, enemies get so fast, that they skip screen edge collider, and simply disappear out of the game. Funny thing to happen, because the original game had the exact same problem, due to enemy position after bounce being miscalculated sometimes. Something I must not forget to fix later._

## Day 12

This time I focused on adding game logic. Handled player collision with enemy, screen edge and food. Soon I will have to introduce concept of scoring, as well as suitable game over screen to show in the end.

After eating food, game spawns another enemy and food at random location. It works, but I am not happy with implementation. Each object references another prefab, in order to dynamically create it or invoke its methods. Will try to look for a better solution, and reduce coupling between objects.

Spent some time investigating disappearing enemy issue. It seems that this is a known limitation of Unity's physics engine, which checks collisions only at fixed points in time. Trying to avoid such behavior by limiting maximum enemy speed. They should not be allowed to go through the edge in one fixed update, so I will set maximum speed to enemy length divided by period of fixed update. Seemed to help, as I don't detect any enemies dropping out of the game area.

## Day 13

Found a way to implement prefab registry, using dedicated singleton object. Reworked all types to use prefab registry, instead of assigning them as properties. I am sure there is an even better way to reference prefabs from game objects, but hey, I am not a professional Unity developer :smile:

## Day 14

Today I mostly dealt with endgame features. Went through a tutorial on how to implement Game Over screen, and added it to Hungry Square. It will be shown on collision with either enemy or screen edge. After finishing the game, it is possible to restart it via menu button. 

_Issue detected: After restarting game, I noticed that keyboard input is not detected anymore, even though player and food objects are spawned correctly. Might be just a minor hiccup because of whole scene reload, but surely something I need to address soon._

## Day 15

Used Game Controller to centralize game speed, instead of spreading it through various scripts. Player and enemy movement will be adjusted based on this value, which is increasing by couple of percents each time a player consumes food. Works well, and I remove enemy speedup on screen edge bounce.

Input is still not working after game restart, event though I tried couple of fixes. Will have to check if it happens in built game as well, but I am starting to get worried.

## Day 16

Since I wanted to test built version anyways, I dedicated all available time today to get CI/CD pipeline up and running. Had a lot of fun discovering how Unity games are built and tested, and how to deploy build output. So much fun actually, that I documented the whole process [in a companion article](./github-workflow-unity-azure-static-webapps.md)

## Day 17

Finally managed to solve non-responsive inputs after game is restarted. Turns out inputs were working fine the whole time, but the game remained paused after scene reload. Adjusted time scale after scene reload, and the game runs as expected. Crisis averted!

Continuing to work on improving overall player experience. Food and enemies were spawning randomly, which sometimes caused immediate collision with player. Adjusted this behavior to a more predictable patter, and made them spawn randomly on the opposite part of playable area. Also started tracking score and displayed it to player on game over.

## Day 18

Encountered an issue on game over screen, happening for built game only. An exception was being thrown, but it was impossible to find out what part of code was causing it. Had to learn how to produce a debug build and expose exception call stack. Turns out an instance of a text field was not properly assigned to game over screen, which caused a NullReferenceException. However, this issue does not arise in play mode. Strange ... Solved it by reassigning text field instance to game over canvas.

## Day 19

Worked on quality of life improvements. Player, food and enemy collision areas were too big, so decided to reduce them to fit actual shape of each object. By increasing main sprite pixels per unit (PPU) a bit, made all relevant objects smaller, to make the game area look a bit bigger. I guess similar could be achieved by moving the camera a bit further from the player.

## Day 20

Added main menu and included a small game description. Initially wanted to implement is the same way as game over screen, but soon realized that it is better to extract it to a separate scene. Main menu scene will be shown first and, once start button is pressed, game scene will be loaded.

## Day 21

Putting in last few tweaks and doing a final round of testing. Everything seems to be in order, and Hungry Square remastered is ready to be shipped!

## Final thoughts

## Conclusion
