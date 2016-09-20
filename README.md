# mazedrivers-api-documentation
Mazedrivers API documentation

# VideumCodeup Mazedrivers

# API

This is the API for the server-side version of Bomberman. Written by [Andreas
Lundahl](https://github.com/andreaslundahl) and [Kevin
Sjöberg](https://github.com/kevinsjoberg) and [Albert
Arvidsson](https://github.com/scmx)

## Action commands

Commands are sent as [JSON](http://json.org/) over
[WebSockets](https://developer.mozilla.org/en-US/docs/WebSockets).

You can for example connect to the websocket via

```javascript
var ws = new WebSocket(`ws://${host}:${port}`)
```

Then you can send a message to it like this:
```javascript
ws.onopen = function () {
  ws.send(JSON.stringify({
    type: "JOIN_REQUEST",
    payload: { nickname: "??" }
  }))
}
```

And listen for all responses like this:
```javascript
ws.onmessage = function (event) {
  console.log(event.data)
}
```

All responses are of the form `{ type: 'SOME_ACTION', payload: 'some data' }`
or `{ type: 'SOME_ERROR', error: true, payload: 'ERROR_DESCRIPTION' }` and
could be handled something like this:

```javascript
  const action = JSON.parse(event.data)
  console.log(action)
  switch (action.type) {
    case 'JOIN_SUCCESS':
      localStorage.setItem('mazedrivers-token', action.payload.token)
      break
    default:
      break
  }
}
```

You start out in spectator-mode when you connect to the socket.

### `JOIN_REQUEST`

Request to join the game with your car. You must specify your nickname.

```json
{"type": "JOIN_REQUEST", "payload": { "nickname": "Your name" } }
```

Error responses:
```json
{"type": "JOIN_FAILURE", "payload": "JOIN_TOO_LATE_GAME_ALREADY_STARTED" }
{"type": "JOIN_FAILURE", "payload": "JOIN_NICKNAME_MISSING" }
{"type": "JOIN_FAILURE", "payload": "JOIN_NICKNAME_INVALID" }
{"type": "JOIN_FAILURE", "payload": "JOIN_NICKNAME_TOO_SHORT" }
{"type": "JOIN_FAILURE", "payload": "JOIN_NICKNAME_TOO_LONG" }
{"type": "JOIN_FAILURE", "payload": "JOIN_NICKNAME_ALREADY_TAKEN" }
```

Success response includes a token that should be used if you need to reconnect
to the websocket server after having reloaded the browser or lost connection.

```json
{"type": "JOIN_SUCCESS", "payload": { "token": "6487d24e-02e4-41ce-8371-db56742d3b6a" } }
```

## `REJOIN_REQUEST`

Request to rejoin the game with the token you got when joining.

```json
{"type": "REJOIN_REQUEST", "payload": { "token": "6487d24e-02e4-41ce-8371-db56742d3b6a"  } }
```

Error responses:
```json

{"type": "REJOIN_FAILURE", "payload": "REJOIN_TOKEN_INVALID" }
{"type": "REJOIN_FAILURE", "payload": "REJOIN_TOKEN_MISSING" }
{"type": "REJOIN_FAILURE", "payload": "REJOIN_TOKEN_WRONG" }
```

Success response:
```json
{"type": "REJOIN_REQUEST", "payload": { "token": "6487d24e-02e4-41ce-8371-db56742d3b6a"  } }
```

## `DRIVE`

Start driving in a direction `EAST`, `WEST`, `NORTH` or `SOUTH`.

```json
{"type": "DRIVE", "payload": "NORTH"}
```

## `BREAK`

Push the breakes to stop the car! (This action does not need a payload

```json
{"type": "BREAK"}
```

## `REVERSE`

Change gear into reverse and drive backwards at a slower pace.

```json
{"type": "REVERSE"}
```
>>>>>>> Initial commit
