---
# layout: page
# title: Multiplayer Twin-Stick Shooter
---

In this project, I wanted to create a prototype for a 2D multiplayer twin-stick shooter - drawing inspiration from games like Enter the Gungeon. My development environment included Godot 4.2 for the game client and Nakama for the server-side logic.

For a deeper dive into the project, feel free to explore the YouTube video and check out the source code on GitHub here: [engine-sandbox-golang](https://github.com/craigbranscom/engine-sandbox-golang)

# Watch the Prototype in Action
Before diving into the technical aspects, check out the video below, where you can see four separate game clients interacting with the same Nakama server. Each client runs on the same machine, and clicking on any window brings it into focus, enabling input for that specific client:

<iframe width="560" height="315" src="https://www.youtube.com/embed/E9laPeN2yeY?si=6qnN7Po5nw9BVZiH" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<br />

##  Core Features

### Multiplayer Setup

Using Nakama as the game server, this project supports multiple clients connected to a shared game state.

* Client Focus Control: Each client listens for input only when in focus. This ensures that keystrokes and mouse clicks are captured correctly without interference from other clients running on the same machine.

* Cursor and Rotation: When a client is in focus, the mouse cursor is replaced with a green circle indicating where the player will fire. The player sprite rotates to always face the reticle. This rotation information is synchronized with the Nakama server, allowing all players to see each other’s orientations.

* Shooting: Clicking anywhere on the map fires a green projectile towards the reticle. The movement of players and projectiles is synchronized across all clients.

* Movement: Players can navigate the map using the WASD keys, and both players and projectiles are networked game objects.

## Network Communication

The Nakama server handles network operations by receiving predefined opcodes from game clients and broadcasting them to all other authenticated clients in the match. Here’s how position updates are sent from a client to the server:

{% highlight gdscript %}
func send_position_op_code(position: Vector2, rotation: float):
    # Craft network message
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

When the Nakama server receives this message it authenticates the sender and relays the message to all other clients. Each client handles the incoming network messages with a match statement that switches based on the opcode:

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

## Opcode Definitions
Each opcode is assigned a unique integer constant shared across all game clients:

{% highlight gdscript %}
const OP_CODE_POSITION = 1
const OP_CODE_READY = 2
const OP_CODE_ATTACK_RANGED = 3
const OP_CODE_PLAYER_DAMAGED = 4
{% endhighlight %}
