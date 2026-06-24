# WebSocket

🖥️ [Slides](https://docs.google.com/presentation/d/1isIXUjbcOjWPD4ZXz5j98t4xns8ImKj9/edit?usp=sharing&ouid=110961336761942794636&rtpof=true&sd=true)

🖥️ [Lecture Videos](#videos)

![webSocket](webServicesWebSocketsLogo.png)

HTTP is based on a client-server architecture where the client always initiates the request and the server responds. This model is ideal for building a global document library connected by hyperlinks, but it is insufficient for many modern use cases. Applications requiring real-time notifications, distributed task processing, peer-to-peer communication, or asynchronous events need communication that can be initiated by any connected device.

For years, web developers used various workarounds to overcome the limitations of the client-server model. These solutions included "polling," where the client frequently pings the server to see if there are updates, or "long polling," where the client opens a connection and keeps it open while waiting for the server to respond with an event. These solutions were often inefficient and difficult to maintain.

In 2011, the WebSocket protocol was standardized to solve this problem. The core feature of WebSocket is that it is **full-duplex**. After an initial connection is made over HTTP and then "upgraded" by the server to a WebSocket connection, the relationship changes to a persistent, bi-directional connection where either party can send data at any time.

![webSocket.png](webSocket.png)

WebSocket connections are strictly between two parties. To facilitate a conversation between a group of users, the server must act as an intermediary. Each peer connects to the server, and the server manages the logic to forward messages among the connected peers.

![peers.png](peers.png)

## Creating a WebSocket Server Connection

The following example demonstrates a basic HTTP server using the `Javalin` library. It supports upgrading to the WebSocket protocol when a client calls the `/ws` endpoint.

```java
import io.javalin.Javalin;

public class SimpleWsEchoServer {
    public static void main(String[] args) {
        Javalin.create()
                .get("/echo/{msg}", ctx -> ctx.result("HTTP response: " + ctx.pathParam("msg")))
                .ws("/ws", ws -> {
                    ws.onConnect(ctx -> {
                        ctx.enableAutomaticPings();
                        System.out.println("WebSocket connected");
                    });
                    ws.onMessage(ctx -> ctx.send("WebSocket response: " + ctx.message()));
                    ws.onClose(ctx -> System.out.println("WebSocket closed"));
                })
                .start(8080);
    }
}
```

This code uses `Javalin.create()` to initialize the server and a fluent API to configure it:
*   `.get()` registers a handler for standard HTTP GET requests.
*   `.ws()` registers handlers for WebSocket lifecycle events (connecting, receiving messages, and closing) at a specific path.
*   `.start(8080)` launches the server on the specified port.

## WebSocket Timeout

By default, if Javalin does not receive communication from a client for 30 seconds, the connection is considered timed out and closed. Once closed, no further messages can be sent without establishing a new connection.

To maintain a persistent connection, we can instruct Javalin to "ping" its clients. If a "pong" is received in response, the connection remains active. This is enabled by calling `ctx.enableAutomaticPings()` within the `onConnect` handler, as shown in the server example above.

## Creating a WebSocket Client Connection

To initiate a WebSocket connection from a Java client, you need a library that implements the `jakarta.websocket.WebSocketContainer` interface (formerly `javax.websocket`). In this course, we use the `Tyrus` library.

> **Install:** `org.glassfish.tyrus.bundles:tyrus-standalone-client:2.1.4`

To handle the connection, you must extend the `jakarta.websocket.Endpoint` abstract class and override the `onOpen` method. You then use a `WebSocketContainer` to connect to the server URI, which returns a `Session` object used for communication.

Incoming messages are handled by registering a `MessageHandler` with the session.

The following code provides a complete client example:

```java
import jakarta.websocket.ContainerProvider;
import jakarta.websocket.Endpoint;
import jakarta.websocket.EndpointConfig;
import jakarta.websocket.MessageHandler;
import jakarta.websocket.Session;
import jakarta.websocket.WebSocketContainer;

import java.io.IOException;
import java.net.URI;
import java.util.Scanner;

public class WsEchoClient extends Endpoint {
    public Session session;

    public static void main(String[] args) throws Exception {
        WsEchoClient client = new WsEchoClient();

        Scanner scanner = new Scanner(System.in);

        System.out.println("Enter a message you want to echo:");
        while(true) {
            client.send(scanner.nextLine());
        }
    }

    public WsEchoClient() throws Exception {
        URI uri = new URI("ws://localhost:8080/ws");
        WebSocketContainer container = ContainerProvider.getWebSocketContainer();
        
        // This call connects to the server and triggers onOpen
        this.session = container.connectToServer(this, uri);

        this.session.addMessageHandler(new MessageHandler.Whole<String>() {
            public void onMessage(String message) {
                System.out.println(message);
                System.out.println("\nEnter another message you want to echo:");
            }
        });
    }

    public void send(String message) throws IOException {
        this.session.getBasicRemote().sendText(message);
    }

    // This method is called when the connection is established
    @Override
    public void onOpen(Session session, EndpointConfig endpointConfig) {
    }
}
```

## Videos

- 🎥 [WebSocket Introduction (10:45)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=fcd3b045-d06e-41f7-8a0e-b1a1014a7b97) - [[transcript]](https://github.com/user-attachments/files/17753884/CS_240_WebSocket_Introduction_Transcript.pdf)
- 🎥 [WebSocket Protocol (7:06)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=91c23bc0-410b-4848-be81-b1a1014dab96) - [[transcript]](https://github.com/user-attachments/files/17753890/CS_240_WebSocket_Protocol_Transcript.pdf)
- 🎥 [Simple WebSocket Example (12:05)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=27a9cc42-fd4a-42ae-b364-b2c80108d66a) - [transcript]
- 🎥 [Pet Shop WebSocket Example (21:21)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=d16b30ba-6d13-4552-84c6-b2c8010c696f) - [transcript]
- 🎥 [Chess WebSocket (18:03)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=aa2f2bd3-cbad-49ca-a0e1-b2cd014e6f2c) - [transcript]