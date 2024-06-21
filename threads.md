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

## Podział zadania na wątki
```java
import java.util.Arrays;

class Worker implements Runnable {
    private final int[] array;
    private final int startIndex;
    private final int endIndex;
    public Worker(int[] array, int startIndex, int endIndex) {
        this.array = array;
        this.startIndex = startIndex;
        this.endIndex = endIndex;
    }
    @Override
    public void run() {
        for (int i = startIndex; i < endIndex; i++)
            array[i]++;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        int numThreads = 3;
        int[] ints = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
        Thread[] threads = new Thread[numThreads];
        int chunkSize = ints.length / numThreads;
        for (int i = 0; i < numThreads; i++) {
            int startIndex = i * chunkSize;
            int endIndex = (i == numThreads - 1) ? ints.length : (i + 1) * chunkSize;
            threads[i] = new Thread(new Worker(ints, startIndex, endIndex));
            threads[i].start();
        }
        for (Thread thread : threads) thread.join();
        Arrays.stream(ints).forEach(System.out::print);

    }
}
```

## Pula wątków

Tworzenie jak najwięcej wątków nie jest najlepszym pomysłem ponieważ zajedziemy procesor przełączaniem kontekstu i nie zyskamy tak naprawdę korzyści z wielowątkowości.
Najbardziej optymalna liczba wątków w puli to ilość dostępnych procesorów w kompie, bo wtedy faktycznie wiele zadań może zostać wykonanych równolegle na każdym CPU.

`executor.awaitTermination()` zatrzymuje główny wątek (blokuje go) i sprawia, że czeka aż wszystkie zadania z puli zostaną zakończone.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Wątek " + Thread.currentThread().getId() + " jest używany.");
    }
}

public class MySample {
    public static void main(String[] args) {
        int cpus = Runtime.getRuntime().availableProcessors();
        ExecutorService executor = Executors.newFixedThreadPool(cpus);

        for (int i = 0; i < 10; i++) {
            MyTask task = new MyTask(); // Runnable class
            executor.execute(task);
        }

        executor.shutdown();
        try {
            executor.awaitTermination(Long.MAX_VALUE, TimeUnit.NANOSECONDS);
        } catch (InterruptedException err) {
            err.printStackTrace();
        }
    }
}
```

Output z pulą wątków o rozmiarze 2:

![image](https://github.com/michaldziuba03/java/assets/43048524/9e853706-6bd3-45ee-961a-0671f2f812c9)

> Zauważ, że po zakończeniu zadania, wątek jest używany ponownie

Można też darować sobie tworzenie klasy implementującą Runnable i przekazać funkcję lambda do executora:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class MySample {
    public static void main(String[] args) {
        int cpus = 3; //Runtime.getRuntime().availableProcessors();
        ExecutorService executor = Executors.newFixedThreadPool(cpus);

        for (int i = 0; i < 10; i++) {
            executor.execute(() -> {
                System.out.println("Wątek " + Thread.currentThread().getId() + " jest używany.");
            });
        }

        executor.shutdown();
        try {
            executor.awaitTermination(Long.MAX_VALUE, TimeUnit.NANOSECONDS);
        } catch (InterruptedException err) {
            err.printStackTrace();
        }
    }
}
```
