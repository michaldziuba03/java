# Java

## Spis treści

- [Operacje na stringach](#operacje-na-stringach)
    - [Długość stringa](#długość-stringa)
    - [Porównanie stringów](#porównanie-stringów)
    - [Formatowanie stringów](#formatowanie-stringów)
    - [Substring](#substring)
    - [Zamiana Stringa na liczbę](#zamiana-stringa-na-liczbę)
- [Parsowanie daty](#parsowanie-daty)
- [Operacje na datach](#operacje-na-datach)
- [Operacje na czasie (godziny, minuty, sekundy)](#operacje-na-czasie)
- [Czytanie pliku po liniach](#czytanie-pliku-po-liniach)
- [Czytanie kolumn lini / rozdzielanie stringa](#czytanie-kolumn-lini--rozdzielanie-stringa)
    - [Użycie .split()](#użycie-split)
- [Sortowanie typów prostych w liście (np. integery)](#sortowanie-typów-prostych-w-liście-np-integery)
- [Sortowanie obiektów w liście](#sortowanie-obiektów-w-liście)
- [Zapisywanie do pliku / pisanie do pliku](#zapisywanie-do-pliku--pisanie-do-pliku)
- [Operacje na folderach](#operacje-na-folderach)
    - [Odczyt po zagnieżdżonych folderach](#odczyt-po-zagnieżdżonych-folderach)

- [Struktury danych](#struktury-danych)
    - [Lista (dynamiczna tablica)](#lista-dynamiczna-tablica)
    - [Hashmap](#hashmap--haszmapa)
    - [HashSet](#hashset)

### Operacje na stringach

```java
// 1. trim
String text = "   Hello World   ";
text.trim(); // "   Hello World  " -> "Hello World"

// 2. toLowerCase
String text = "HELLO WORLD";
text.toLowerCase(); // "hello world"

// 3. toUpperCase
String text = "hello world";
text.toUpperCase() // "HELLO WORLD"

// 4. replace
String text = "21,37";
text.replace(",", ".") // "21.37"

String text = "cat cat cat";
text.replace("cat", "dog"); // "dog dog dog"

// 5. replaceFirst
String text = "cat cat cat";
text.replaceFirst("cat", "dog"); // "dog cat cat"
```

> Uwaga - powyższe metody zwracają NOWEGO stringa bez modyfikacji oryginalnego.

#### Długość stringa
```java
"123".length() // 3
```

#### Porównanie stringów

Lepiej używać .equals() niż == do porównywania stringów
```java
String name = "John";

if (name.equals("John")) {
    System.out.println("Lepiej używać .equals niż == do porównywania stringów");
}

// inne przydatne metody zwracające wartość logiczną:
String text = "Hello World";

text.startsWith("Hello"); // true
text.endsWith("World"); // true
text.contains("llo"); // true
text.isEmpty(); // false
```

#### Formatowanie stringów
```java
String name = "John Doe";
String.format("My name is: %s", name) // My name is: John Doe

// formatowanie ułamków - %.1f oznacza jedno miejsce po przecinku, %.2f oznaczałoby 2 miejsca itd...
// Locale.ENGLISH jest używane by ułamek był oddzielany przez kropkę zamiast przecinek (domyślnie)
double x = 21.37;
String.format(Locale.ENGLISH, "x=%.1f", x); // x=21.4

int x = 21;
String.format(Locale.ENGLISH, "x=%d", x); // x=21

// Łączenie stringów
"Hello, my name is: " + name // Hello, my name is John Doe
```
#### Substring
```java
String text = "Test: 233";
text.substring(6) // "233"
text.substring(0, 4) // "Test"
```

#### Zamiana Stringa na liczbę
```java
Integer.parseInt("500") // 500 ale jako int
Double.parseDouble("500.244") // 500.244 jako double
```

Czasami dane mogą zawierać ułamek z przecinkiem więc musimy to przekształcić przed parsowaniem: 

```java
String someData = "200,4";
Double.parseDouble(someData.replace(",", ".")); // 200.4 jako double
```

### Parsowanie daty

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class DateUtils {
    private static final String DATE_FORMAT = "M/d/yy"; // np. 4/6/20
    
    public static LocalDate toDate(String dateStr) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern(DATE_FORMAT);
        return LocalDate.parse(dateStr, formatter);
    }

    public static String toStringDate(LocalDate date) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern(DATE_FORMAT);
        return formatter.format(date);
    }
}
```
### Operacje na datach

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
LocalDate otherDate = LocalDate.parse("10-07-2010", formatter);
LocalDate today = LocalDate.now(); // 2024-04-26 (pobranie dzisiejszej daty)

// porównywanie dat:
today.isAfter(otherDate); // true
today.isBefore(otherDate); // false

// naiwne obliczenie różnicy lat
int diff = today.getYear() - otherDate.getYear();
System.out.printf("Jest %d lat różnicy\n", diff); // "Jest 14 lat różnicy"

// dokładniejsze obliczenie różnicy lat
int diff2 =  Period.between(otherDate, today).getYears();
System.out.printf("Jest %d lat różnicy\n", diff2); // "Jest 13 lat różnicy"
```

### Operacje na czasie 

```java
LocalTime time = LocalTime.of(23, 2, 2); // 23:02:02

DateTimeFormatter formatter =  DateTimeFormatter.ofPattern("HH:mm:ss"); // HH - oznacza 24h format
DateTimeFormatter formatter12h = DateTimeFormatter.ofPattern("h:mm:ss a", Locale.ENGLISH); // hh - 12h format

formatter.format(time); // "23:02:02"
formatter12h.format(time); // "11:02:02 PM"

LocalTime now = LocalTime.now(); // 21:33:51
// formatowanie obecnego czasu
formatter.format(now); // "21:33:51"

now.isAfter(time); // false
now.isBefore(time); // true
```

### Czytanie pliku po liniach

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.Iterator;
import java.util.stream.Stream;

public class ReadFile {
    public static void readLines(String filename) throws IOException {
        Path path = Paths.get(filename);

        if (Files.notExists(path)) { // albo: !Files.exists(path)
            // sprawdzenie czy plik nie istnieje (czasem wymagane w zadaniach)
            System.out.println("Plik nie istnieje");
        }

        Stream<String> lines =  Files.lines(path);

        // Kilka sposobów na iteracje po liniach

        // 1. Lambda
        lines.forEach((line) -> {
            System.out.println(line);
        });

        // 2. Referencja do metody
        lines.forEach(System.out::println);

        // 3. Iterator
        Iterator<String> it = lines.iterator();

        while(it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

#### Przekształcanie, filtrowanie linijek

Więcej przykładów: [Stream guide](https://stackify.com/streams-guide-java-8/)

```java
Path path = Paths.get(filename);
Stream<String> lines =  Files.lines(path);

lines.skip(100) // jeżeli chcemy pominać 100 linijek
    .map(String::toLowerCase) // jeżeli chcemy każdą pojedynczą linię przekształcić
    .filter(line -> line.startsWith("...")) // filtrowanie linijek po zawartości
```

### Czytanie kolumn lini / rozdzielanie stringa

```java
import java.util.StringTokenizer;

public class ReadColumns {
    private static final String DELIMITER = ";"; // przyjrzyjcie sie dokładnie jaki jest separator w pliku .csv (zazwyczaj "," ";" "|")
    
    public static void splitLine(String line) {
        StringTokenizer tokenizer = new StringTokenizer(line, DELIMITER);

        // tokenizer działa podobnie jak iterator
        while (tokenizer.hasMoreTokens()) {
            System.out.println(tokenizer.nextToken());
        }
    }
}
```
#### użycie .split()

```java
String text = "Germany;Poland;Ukraine";
String[] countries = text.split(";");

System.out.println(countries[0]);
System.out.println(countries[1]);
System.out.println(countries[2]);
```

Sugeruję zawsze jeżeli to możliwe dać drugi parametr gdzie uwzględniamy limit elementów. 

```java
// UWAGA - Pułapka
String text = "Germany;;";
String[] countries = text.split(";");

System.out.println(countries[0]);
System.out.println(countries[1]); // ERROR
System.out.println(countries[2]);

// FIX:
String text = "Germany;;";
String[] countries = text.split(";", 3);

System.out.println(countries[0]);
System.out.println(countries[1]);
System.out.println(countries[2]);
```

### Sortowanie typów prostych w liście (np. integery)

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Sort {
    List<Integer> list;

    Sort() {
        list = new ArrayList<Integer>();
        list.add(6);
        list.add(2);
        list.add(100);
        list.add(4);
    }
  
    public void simpleSort() {
        // Collections.sort modyfikuje przekazaną listę
        Collections.sort(list); // [2, 4, 6, 100]
        System.out.println(list);

        // Collections.reverse NIE SORTUJE - po prostu odwraca nam listę
        Collections.reverse(list); // [100, 6, 4, 2]
        System.out.println(list);
    }
}
```

### Sortowanie obiektów w liście

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

public class Sort {
    List<DeathStat> list;

    Sort() {
        list = new ArrayList<DeathStat>();
        list.add(new DeathStat("Dęblin", 10));
        list.add(new DeathStat("Ryki", 5));
        list.add(new DeathStat("Lublin", 100));
        list.add(new DeathStat("Puławy", 20));
    }
    public void objectSort() {
        // Używamy komparatora - musimy przekazać lambdę/referencje do metody która zwraca inta (dla .comparingInt)
        // jeżeli chcemy posortować (najmniejszy -> największy)
        list.sort(Comparator.comparingInt(DeathStat::getCount));

        // jeżeli chcemy posortować odwrotnie (największy -> najmniejszy)
        list.sort(Comparator.comparingInt(DeathStat::getCount).reversed());
    }
}
```

```java
public class DeathStat {
    private final int count;
    private final String city;

    DeathStat(String city, int count) {
        this.count = count;
        this.city = city;
    }

    int getCount() {
        return this.count;
    }
}
```

### Zapisywanie do pliku / pisanie do pliku

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class SaveFile {
    public static void save(String filename) throws IOException {
        BufferedWriter writer = new BufferedWriter(new FileWriter(filename));

        writer.write("<body>\n");
        writer.write("<h1>Hello World</h1>\n");
        writer.write("</body>");

        writer.close(); // WAŻNE: jak zapomnimy zamknąć bufora to nie zobaczymy nic w pliku
    }
}
```

### Operacje na folderach

Powiedzmy, że mam taki folder i chcę wyświetlić tylko pliki .txt:

![image](https://github.com/michaldziuba03/java/assets/43048524/20803306-002d-42fa-bc27-176dd430eb6c)


```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

public class Main {
    public static void main(String[] args) throws IOException {
        Path dirPath = Paths.get("mydir");

        Files.isDirectory(dirPath); // true

        // przeszukiwanie folderu:
        // maxDepth = 1 - czyli przeszukuje 1 poziom zagnieżdżenia folderów
        Files.find(dirPath, 1, (path, __) -> {
            return path.getFileName().toString().endsWith(".txt") && Files.isRegularFile(path);
        }).forEach((path) -> System.out.println(path.getFileName()));
        
        /* OUTPUT:
            file1.txt
            file2.txt
        */
    }
}
```

#### Odczyt po zagnieżdżonych folderach

![image](https://github.com/michaldziuba03/java/assets/43048524/786fffe1-7e18-45bc-9757-1621021ae523)


```java
Path dirPath = Paths.get("mydir");

// maxDepth = 2 - czyli przeszukuje 2 poziomy zagnieżdżenia folderów
Files.find(dirPath, 2, (path, __) -> {
    return path.getFileName().toString().endsWith(".txt") && Files.isRegularFile(path);
}).forEach((path) -> System.out.println(path.getFileName()));
/* OUTPUT:
    file1.txt
    file2.txt
    file3.txt
*/
```

## Struktury danych

### Lista (dynamiczna tablica)

Struktura danych, która przechowuje elementy w uporządkowany sposób. Można sobie ją wyobrazić jako pudełka, które ułożone są obok siebie. Każde pudełko ma swoją własną etykietę, czyli numer, którym można się do niego odnieść.

```java
import java.util.ArrayList;
import java.util.List;

public class TodoList {
    List<String> todo;

    TodoList() {
        todo = new ArrayList<>();

        // dodawanie elementów
        todo.add("Wyjście z psem");
        todo.add("Nauka na asyko");
        todo.add("Nauka na porawke z asyko");
        todo.add("Kąpiel z podłączonym tosterem");

        // usunięcie pierwszego elementu
        todo.remove(0); // [Nauka na asyko, Nauka na porawke z asyko, Kąpiel z podłączonym tosterem]
        
        // pobranie pierwszego elementu
        todo.get(0); // "Nauka na asyko"
        
        // sprawdzenie czy tablica zawiera element
        todo.contains("Nauka na asyko"); // true
        
        // usunięcie z filtrowaniem
        todo.removeIf((item) -> item.startsWith("Nauka")); // [Kąpiel z podłączonym tosterem]

        todo.clear(); // wyczyszczenie całej listy
    }
}
```

#### Iterowanie po liście obiektów (instancji klas)
```java
List<Country> countries = new ArrayList<>();

[...]

for (Country country : countries) {
    System.out.println(country.name);
}
```

### Hashmap / haszmapa

Struktura danych, która przechowuje pary klucz-wartość. Przykładowo możesz myśleć o niej jak o książce adresowej, gdzie kluczem jest nazwa osoby, a wartością jest jej numer telefonu.

```java
import java.util.HashMap;
import java.util.NoSuchElementException;

public class DiseasesDescriptions {
    private final HashMap<String, String> descriptions;

    DiseasesDescriptions() {
        descriptions = new HashMap<>();
        descriptions.put("diarrhea", "condition characterized by frequent [...]");
        descriptions.put("ebola", "severe and often fatal illness caused by the Ebola virus");
        descriptions.put("coronavirus", "infectious respiratory illness caused by the severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2). ");
    }

    String getDescription(String diseaseName) {
        if (!this.descriptions.containsKey(diseaseName)) {
            throw new NoSuchElementException("Not found");
        }
        return this.descriptions.get(diseaseName);
    }
}
```

### HashSet

Struktura działa podobnie do listy ale zawsze przechowuje unikalne elementy. Czyli nie ważne ile razy wykonamy `.add()` z tym samym elementem, to nigdy nie będziemy mieli duplikatów. 

> Uwaga - kolejność elementów w trakcie iteracji po hash secie nie jest gwarantowana.

> Uwaga - nie da sie pobrać elementów z hash setu po indexie - jeżeli nam zależy na operowaniu na indexach to możemy utworzyć tablicę na bazie hash setu (pokazane w następnym przykładzie).

```java
import java.util.HashSet;

public class Main {
    public static void main(String[] args) {
        HashSet<String> cars = new HashSet<String>();
        cars.add("Volvo");
        cars.add("BMW");
        cars.add("Ford");
        cars.add("BMW"); // dodajemy drugi raz "BMW", ale to nie wpływa na dane w hash secie
        cars.add("Mazda");

        System.out.println(cars); // [Volvo, Mazda, Ford, BMW]

        cars.contains("Mazda"); // true
        cars.remove("Volvo"); // [Mazda, Ford, BMW]

        cars.clear(); // czyści cały hash set

        // iteracja po elementach
        for (String car : cars) {
            System.out.println(car);
        }
    }
}
```

#### Utworzenie listy na bazie hash setu

HashSet może być przydatny gdy kolekcjonujemy dane które muszą być unikalne i nie zależy nam na kolejności. Możemy utworzyć tablicę na podstawie hash setu by potem już operować jak na zwykłej liście. 

```java
        Set<String> cars = new HashSet<>();
        cars.add("Volvo");
        cars.add("BMW");
        cars.add("Ford");
        cars.add("BMW"); // dodajemy drugi raz "BMW", ale to nie wpływa na dane w hash secie
        cars.add("Mazda");
        List<String> carsList = new ArrayList<>(cars);

        for (String car : carsList) {
            System.out.printf("Car: %s\n", car);
        }
```
