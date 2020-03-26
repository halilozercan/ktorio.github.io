---
title: WebSockets
category: clients
permalink: /clients/websockets.html
caption: WebSockets
feature:
    artifact: io.ktor:ktor-client-websockets:$ktor_version,io.ktor:ktor-client-cio:$ktor_version,io.ktor:ktor-client-js:$ktor_version,io.ktor:ktor-client-okhttp:$ktor_version
    class: io.ktor.client.features.websocket.WebSockets
ktor_version_review: 1.2.0
---

{% include feature.html %}

Ktor provides a WebSocket client for the following engines: CIO, OkHttp, Js. To get more information about the server side, follow this [section](/servers/features/websockets.html).

Once connected, client and server WebSockets share the same [WebSocketSession](/servers/features/websockets.html#WebSocketSession)
interface for communication.

The basic usage to create an HTTP client supporting WebSockets is pretty simple:

```kotlin
val client = HttpClient {
    install(WebSockets)
}
```

Once created we can perform a request, starting a `WebSocketSession`:

```kotlin
client.ws(
    method = HttpMethod.Get,
    host = "127.0.0.1",
    port = 8080, path = "/route/path/to/ws"
) { // this: DefaultClientWebSocketSession

    // Send text frame.
    send("Hello, Text frame")

    // Send text frame.
    send(Frame.Text("Hello World"))

    // Send binary frame.
    send(Frame.Binary(...))

    // Receive frame.
    val frame = incoming.receive()
    when (frame) {
        is Frame.Text -> println(frame.readText())
        is Frame.Binary -> println(frame.readBytes())
    }
}
```

WebSocketSession does not start a ping-pong exchange with the server by default. To enable health check through ping-pong messages, we provide an initial configuration while installing the feature

```
val client = HttpClient {
    install(WebSockets) {
        pingIntervalMillis = 15000L // Period of each ping message. 
        timeoutMillis = 15000L // Timeout to receive a pong message.
    }
}
```

For more information about the WebSocketSession, check the [WebSocketSession page](/servers/features/websockets.html#WebSocketSession) and the [API reference](https://api.ktor.io/{{ site.ktor_version }}/io.ktor.client.features.websocket/).
