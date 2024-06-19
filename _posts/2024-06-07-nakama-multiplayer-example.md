---
layout: post
title:  "Twin-Stick Multiplayer Shooter with Nakama RT API"
date:   2024-06-07 13:18:31 -0400
categories: nakama godot multiplayer
---

This prototype is a multiplayer twin-stick shooter powered by the Nakama RT API and implements several of Nakama's social and login features.

Players create an account and login to authenticate a game session. From there they can create or join a lobby of players, and once at least 2 players are in a lobby the game can begin.

Players are able to control their character using WASD and fire their weapon with a left click. Once a weapon has run out of ammo it must be reloaded with a right click. Hitting the other player deals damage and when a player's health reaches zero they have been defeated and must respawn.

In the video below you will see 2 separate game client instances logging in, setting up a lobby, and launching a real-time multiplayer match.

<iframe width="560" height="315" src="https://www.youtube.com/embed/s3dWk6ibt50?si=sIKM7j20WjtzxCC7" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

