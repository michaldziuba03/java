## Wielowątkowość

Czasem musimy wykonać operacje która trwa potencjalnie długo i tym samym nie blokować dalszego wykonywania aplikacji - możemy taką operacje oddelegować do oddzielnego wątku. 

Wielowątkowość w Javie można osiągnąć na wiele sposobów:

### Rozszerzanie Thread

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

### Implementowanie Runnable

Drugi sposób to implementowanie klasy `Runnable` i potem przekazywanie jej do konstruktora `Thread`.
Daje to większą elastyczność i pozwala na używanie klasy w wielu kontekstach, może również dziedziczyć po innych klasach.

```java
public class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Wątek " + Thread.currentThread().getId() + " jest uruchomiony.");
    }

    public static void main(String[] args) {
        int numberOfThreads = 5; // Liczba wątków do uruchomienia
        for (int i = 0; i < numberOfThreads; i++) {
            Thread thread = new Thread(new MyRunnable());
            thread.start(); // Rozpoczyna wykonanie w nowym wątku
        }
    }
}
```
