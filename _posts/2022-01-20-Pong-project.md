---
layout: portfolio_entry
title: Pong
image: /img/Pong/Thumbnail.png
permalink: blog/pong
github-link: https://github.com/HarryNguyen1998/PongInUnity
---

This is a Unity implementation of Pong, the classic arcade game released in 1972. You can play a WebGL version of the game [here](https://harrynguyen.itch.io/pong-clone)


![](/img/Pong/gameplay-preview.gif)

### Introduction
I have been tingling with game engines like UE4 and Unity for quite a while, but I never got into
making a fully "complete" game. So I tried creating Pong without any guides (I still search for
Unity references and manual, just not how to make the game). Heck, let's also learn the new [Unity input system][1].
My naive mind thought that it would take only 1 week. Oh boy, was I so wrong!


On top of my mind, the features are:
- A Main Menu that could transition into Gameplay, Settings, or Quit
- 2 modes: play vs AI or vs other player
- The game loop, where 2 paddles can only move in 1 axis to bounce the ball back, and hopefully out
  the other player
- GameOver scene, which displays the final score


It doesn't sound like a lot, but when you actually want to make it a "complete" game, as in fully
functional with some code architecture, I learned a lot, especially on how to connect everything
together. After this project, I sort of have a good sense on how to architecture my code for future
projects. It was a wild experience!

Some pictures of the game:
<table>
<tr>
  <td> <img src="/img/Pong/Thumbnail.png" alt="Menu" style="width: 125px; text-align: center; vertical-align: middle;"/>
  <figcaption>Menu</figcaption>
  </td>
  <td> <img src="/img/Pong/Gameplay.png" alt="Gameplay" style="width: 125px; text-align: center; vertical-align: middle;"/>
  <figcaption>Gameplay</figcaption>
  </td>
  <td> <img src="/img/Pong/GameOver.png" alt="flat-shading" style="width: 125px; text-align: center; vertical-align: middle;"/>
    <figcaption>GameOver</figcaption></td>
</tr>
</table>


### Postmortem
#### Preload scene
I heard about [preload scene](https://stackoverflow.com/questions/35890932/unity-game-manager-script-works-only-one-time/35891919#35891919) and it was ok
to make sure things like `GameManager` runs first without messing with the Script Execution Order
settings. The problem occurs when you have more then 1 scene, and you have to get into the Preload
scene 1st before the others. Thus, I consulted the search engine and found that I could instantiate
a Prefab before anything else. Basically, you put all your globals inside a prefab, e.g., `Main`.
Then, you create a static function with `RuntimeInitializeOnLoadMethod` attribute that loads that
prefab (for more details, see this awesome [post][2])


#### Unity new input system
I wanted to make local multiplayer where both players access the same device, e.g., 1 player moves
the pad with WASD, while the other with arrows. This translates to having 2 GameObject accessing the
same device. The other consideration was to slap in the AI when we change settings.

1st attempt: I created `AIController` and `PlayerController`, with a manager called `ControllerSwitcher` that
handles the activation of which controller. Each GameObject also contains reference to the input actions
called `GameInput` to map to action responses. However, this doesn't work since the default pairing
mechanism is that each player connects to a different device.

2nd attempt: Create 2 control scheme and the `ControllerSwitcher` is responsible for binding the
correct scheme to each controller. This seems to work, until you realize that if you use a global
action, e.g., Pause, it doesn't work. I suspected that the problem was that when it was clicked, the
button was called twice, aka it was opened then closed immediately. I did try to make sure that only
1 action response is listened, but that became a nightmare with all the ifs, tags.  Furthermore,
going down the rabbit hole I found that if I let both AIs take control, the button just doesn't work
anymore!

3rd attempt: After going back to the drawing board and watching a couple of tutorials, I realized
that I could centralize the inputs into a separate GameObject! So, I created an `InputHandler` and
put inside a separate GameObject. It references the relevant actors with `SerializeField` attribute,
binds the action responses, and it works fine so far.

#### Unity Animator as game states
Another thing that I learned during how to connect everything together is Finite State Machine, a
great way to separate your code. And, through this [tutorial][3] that I stumbled upon, everything
just clicked. The idea is to decouple the state transition from the actual action performed in that
state, by letting the animator handles the state transition. With this method, we have a good visual
look at what's going in while debugging the game
![](/img/Pong/animator-as-state-machines.png)


#### Conclusion
In the end, this was the flow that I came up with for my game. As I learn more, things will change
but so far this works good enough:

<embed src="/img/Pong/state-transition.svg" type="image/svg+xml" />


## Resources
- [Global script initialization](https://low-scope.com/unity-tips-1-dont-use-your-first-scene-for-global-script-initialization/)
- [State machine using Unity animator](ttps://github.com/lumpn/gamedev-workshop/tree/master/StateMachines)

[1]: https://docs.unity3d.com/Packages/com.unity.inputsystem@1.3/manual/index.html
[2]: https://low-scope.com/unity-tips-1-dont-use-your-first-scene-for-global-script-initialization/
[3]: https://github.com/lumpn/gamedev-workshop/tree/master/StateMachines