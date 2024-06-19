---
layout: post
title:  "Real-time Networked Godot Objects with Nakama Server"
date:   2024-05-22 13:18:31 -0400
categories: nakama godot multiplayer
testvar: 5
---
For this project I wanted to prototype a 2D multiplayer twin-stick shooter game similar to Enter the Gungeon. I chose to write the game client using Godot 4.2 and
to use Nakama for the game server.

In the video below you can see 4 separate game clients running against the same Nakama server. Clicking on a window brings it into focus and activates the input
listener for that client - this prevents keystrokes and mouse clicks from being picked up by the other clients since they're all running on the same machine.

<iframe width="560" height="315" src="https://www.youtube.com/embed/E9laPeN2yeY?si=6qnN7Po5nw9BVZiH" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<br />

When a game client is in focus the mouse cursor is replaced by a green circle indicating where the player will fire their weapon. The player sprite will also rotate
so its always "looking at" the reticle. This rotation is tracked on the Nakama server as well so all other players will be able to see the orientation of all other players in
the same match.

Clicking anywhere on the map will cause the player sprite to shoot a green projectile towards the reticle. Players can also move
around the map with the WASD keys. The players and green projectiles are both networked game objects so they are tracked by the Nakama server and can interact with each other.

The Nakama server receives predefined network opcodes from each game client that is then relayed to every other authenticated game client participating in the match.
For example, here is the function that a game client calls to tell the Nakama server about its latest position data:

{% highlight gdscript %}
func send_position_op_code(position: Vector2, rotation: float):
	# craft network message
	var msg = {
		X = position.x,
		Y = position.y,
		Rotation = rotation
	}
	await ServerClient.socket.send_match_state_async(
		ServerClient.lobby.match_id, 
		ServerClient.OP_CODE_POSITION, 
		JSON.stringify(msg))
{% endhighlight %}

From here, the Nakama server receives this message, authenticates the sender, then relays this message to the other 3 game clients who handle the network message with
the following function handler, which is simply a GDScript match statement that switches on each opcode:

{% highlight gdscript %}
func _on_match_state(match_state: NakamaRTAPI.MatchData):
	print("Received new match state from", match_state.presence.username)
	match match_state.op_code:
		ServerClient.OP_CODE_POSITION:
			var msg = JSON.parse_string(match_state.data)
			position_op_code_received.emit(match_state.presence.username, msg["X"], msg["Y"], msg["Rotation"])
		ServerClient.OP_CODE_ATTACK_RANGED:
			var msg = JSON.parse_string(match_state.data)
			var vec: Vector2 = string_to_vector2(msg["Direction"])
			attack_ranged_op_code_received.emit(match_state.presence.username, vec)
		ServerClient.OP_CODE_PLAYER_DAMAGED:
			var msg = JSON.parse_string(match_state.data)
			print(msg)
			player_damaged_op_code_received.emit(msg["PlayerDamaged"], msg["Damage"])
		_:
			print("Unsupported Op Code: %s" % match_state.op_code)
{% endhighlight %}

<br />

Each opcode is assigned an integer value constant that all other game clients share:

{% highlight gdscript %}
const OP_CODE_POSITION = 1
const OP_CODE_READY = 2
const OP_CODE_ATTACK_RANGED = 3
const OP_CODE_PLAYER_DAMAGED = 4
etc...
{% endhighlight %}