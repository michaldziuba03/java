## Minimalny server z możliwością broadcastowania

Klientów obsługujemy w oddzielnych wątkach by uniknąć blokowania i móc ich obsługiwać wielu równolegle.

```java
package server;

import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;
import java.util.List;

public class MyServer {
    private ServerSocket listener;
    private List<ClientHandler> clients = new ArrayList<>();

    public MyServer(int port) {
        try {
            this.listener = new ServerSocket(port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //
    public void listen(){
        System.out.println("Oczekiwanie...");
        while(true){
            Socket socket;
            try {
                socket = listener.accept();
                System.out.println("Nowy klient dołączył!");
                ClientHandler client = new ClientHandler(socket, this);
                clients.add(client);
                client.start();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }

    public void broadcast(String message){
        for(var currentClient : clients)
            currentClient.send(message);
    }

    public void remove(ClientHandler handler) {
        this.clients.remove(handler);
    }
}
```

```java
package server;

import java.io.*;
import java.net.Socket;

public class ClientHandler extends Thread {
    private Socket socket;
    MyServer server;
    private PrintWriter writer;
    ClientHandler(Socket socket, MyServer server) {
        this.socket = socket;
        this.server = server;
    }
    Socket getSocket() {
        return socket;
    }

    public void send(String message){
        writer.println(message);
    }

    @Override
    public void run() {
        try {
            InputStream input = socket.getInputStream();
            OutputStream output = socket.getOutputStream();
            BufferedReader reader = new BufferedReader(new InputStreamReader(input));
            writer = new PrintWriter(output, true);

            String message;
            while ((message = reader.readLine()) != null){
                System.out.println(message);
                if (message.equals("broadcast")) {
                    server.broadcast("do wszystkich: " + message);
                } else {
                    send("Echo: " + message);
                }
            }

            System.out.println("Rozłączono.");
            server.remove(this);
        } catch (IOException err) {
            err.printStackTrace();
        }
    }
}
```
