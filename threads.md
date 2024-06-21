## Wielowątkowość

### Najprostszy przykład

Jednym ze sposobów jest utworzenie klasy rozszerzającą `Thread` z zaimplementowaną metodą `.run()`.
By rozpocząc wykonywanie wątków należy wywołać metodę `.start()` klasy rozszerzającą `Thread`.

```java
public class MyThread extends Thread {
    public void run() {
        System.out.println("Wątek " + Thread.currentThread().getId() + " jest uruchomiony.");
    }

    public static void main(String[] args) {
        int numberOfThreads = 5; // Liczba wątków do uruchomienia
        for (int i = 0; i < numberOfThreads; i++) {
            MyThread thread = new MyThread();
            thread.start(); // Rozpoczyna wykonanie w nowym wątku
        }
    }
}
```
