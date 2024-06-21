## Minimalny klient który odczytuje z konsoli i wysyła na serwer

```java
import java.io.*;
import java.net.Socket;

public class Client extends Thread {
    private Socket socket;
    private PrintWriter writer;
    Client(String hostname, int port) {
        try {
            socket = new Socket(hostname, port);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void run() {
        try {
            InputStream input = socket.getInputStream();
            OutputStream output = socket.getOutputStream();
            BufferedReader reader = new BufferedReader(new InputStreamReader(input));
            writer = new PrintWriter(output, true);
            String message;
            while ((message = reader.readLine()) != null){
                System.out.println(message);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void send(String message){
        writer.println(message);
    }
}
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class MainClient {
    public static void main(String[] args) {
        Client client = new Client("localhost", 3000);
        client.start();

        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));

        try {
            while(true) {
                String message = reader.readLine();
                client.send(message);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
