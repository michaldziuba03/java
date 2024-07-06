
# Podstawy Javy

## Typy proste vs złożone (Obiekty)

Typy proste w Java to:
- byte
- short
- int
- long
- float
- double
- char
- boolean

Typy złożone zaś to wszelkie tablice, klasy zdefiniowane przez użytkownika. Wbudowany String również jest typem złożonym.

### Porównanie

| Właściwość                | Typ prosty                    | Typ złożony  (Obiekty)            |
|---------------------------|-------------------------------|-----------------------------------|
| **Przechowywanie w pamięci** | Stos    (stack)            | Sterta (heap)                     |
| **Przekazywanie do metod** | Przez wartość  (jest kopiowane)  | Przez referencję  (referencja jest kopiowana ale nie wartość na którą wskazuje)   |
| **Domyślna wartość**        | 0 (dla typów liczbowych),       | `null`                            |
|                             | `false` (dla `boolean`),        |                                   |
|                             | '\u0000' (dla `char`)           |                                   |
| **Przykłady**               | `int`, `double`, `boolean`, `char` | `String`, `int[]`, `ArrayList<Integer>`, `MyClass` |
| **Czy może być `null`?**    | Nie                            | Tak                               |

### Jak to działa w praktyce

Powiedzmy, że mamy taką klasę - posłuży nam ona do badania jak obiekty sie zachowują gdy są przekazywane przez parametr do metod.

```java
class Person {
    public String name;
    public String email;

    Person(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

W poniższym przykładzie tworzę 3 zmienne: String (obiekt), int (typ prosty) i klasę (obiekt) i przekazuje do odpowiednich metod które je modyfikują.

Int jest kopiowany, więc w metodzie tworzona jest na stosie lokalna kopia, która potem jest modyfikowana ale oryginalna zmienna pozostaje bez zmian.

Klasa `Person` jest przekazywana do metody jako referencja (tak naprawdę referencja jest kopiowana), więc wszelkie zmiany będą widoczne również w oryginalnej zmiennej.

String jest również przekazywany jako referencja ale jest on zachowuje sie w specyficzny sposób - gdy modyfikujemy Stringa, tak naprawdę tworzymy nowego Stringa, ponieważ String jest niemutowalny. Wyobraź sobie, że gdy przekazujesz Stringa, to kopiowana jest jego referencja, potem ta kopia referencji jest nadpisywana i wskazuje na nowo utworzony String `"Michał"`. Z tego powodu oryginalna zmienna pozostaje bez zmian.

*Ważne rzeczy do zrozumienia*:

Trzymamy na stosie (stack) referencje do obiektu który trzymany jest na stercie (heap). Gdy przekazujemy obiekt do metody, to ta referencja trzymana na stosie jest kopiowana. W przypadku Stringa który jest niemutowalny,
gdy przypisujemy do niego nową wartość to tworzony jest na stercie nowy obiekt i nasza kopia referencji wtedy wskazuje na ten nowy obiekt.

```java
public class Main {
    public static void main(String[] args) {
        String name = "John";
        int num = 10;
        Person person = new Person("John", "john@example.com");

        changeNumber(num);
        changePerson(person);
        changeString(name);

        System.out.println(num); // 10
        System.out.println(person.name); // Michał
        System.out.println(person.email); // mail@michaldziuba.dev
        System.out.println(name); // John

    }

    public static void changeString(String str) {
        // pułapka - mimo, że jest referencją, String jest immutable
        str = "Michał";
    }

    public static void changeNumber(int num) {
        num = 200;
    }

    public static void changePerson(Person p) {
        p.setName("Michał");
        p.setEmail("mail@michaldziuba.dev");
    }
}
```

# Wzorce projektowe

## Builder

Jest to jeden z wzorców kreacyjnych. Stosujemy go głównie w celu uniknięciu dużych i skomplikowanych konstruktorów klas.

Powiedzmy, że mamy klasę która reprezentuje kanapkę:

```java
public class Sandwich {
    private String bread;
    private String meat;
    private String cheese;
    private String vegetables;

    public Sandwich(String bread, String meat, String cheese, String vegetables) {
        this.bread = bread;
        this.meat = meat;
        this.cheese = cheese;
        this.vegetables = vegetables;
    }

    @Override
    public String toString() {
        return "Sandwich{" +
                "bread='" + bread + '\'' +
                ", meat='" + meat + '\'' +
                ", cheese='" + cheese + '\'' +
                ", vegetables='" + vegetables + '\'' +
                '}';
    }
}
```

Klasa budowniczego która zawrze logikę tworzenia nowej klasy:

```java
public class SandwichBuilder {
    private String bread;
    private String meat;
    private String cheese;
    private String vegetables;

    public SandwichBuilder setBread(String bread) {
        this.bread = bread;
        return this;
    }

    public SandwichBuilder setMeat(String meat) {
        this.meat = meat;
        return this;
    }

    public SandwichBuilder setCheese(String cheese) {
        this.cheese = cheese;
        return this;
    }

    public SandwichBuilder setVegetables(String vegetables) {
        this.vegetables = vegetables;
        return this;
    }

    public Sandwich build() {
        return new Sandwich(bread, meat, cheese, vegetables);
    }
}
```

Użycie:

```java
public class Main {
    public static void main(String[] args) {
        Sandwich mySandwich = new SandwichBuilder()
                .setBread("Whole grain")
                .setMeat("Turkey")
                .setCheese("Cheddar")
                .setVegetables("Lettuce, Tomato")
                .build();

        System.out.println(mySandwich);
    }
}
```

Zalety:
- Możesz konstruować obiekty etapami, odkładać niektóre etapy, lub wykonywać je rekursywnie.
- Możesz wykorzystać ponownie ten sam kod konstrukcyjny budując kolejne reprezentacje produktów.
- Zasada pojedynczej odpowiedzialności. Można odizolować skomplikowany kod konstrukcyjny od logiki biznesowej produktu.

Wady:
- Kod staje się bardziej skomplikowany, gdyż wdrożenie tego wzorca wiąże się z dodaniem wielu nowych klas.

# Kolekcje

## Omów interfejs `Collection`

Iterfejs Collection reprezentuje zbior obiektów, znanych jako elementy kolekcji. Stanowi on bazę dla wielu innych interfejsów i klas kolekcji w Javie.

Cechy:

- Interfejs generyczny: Pozwala na określenie typu obiektów, które będą przechowywane w kolekcji, co zapewnia bezpieczeństwo typów.
- Operacje podstawowe: Zapewnia metody do podstawowych operacji, takich jak dodawanie, usuwanie, sprawdzanie rozmiaru i sprawdzanie, czy kolekcja jest pusta.
- Iteracja: Umożliwia iterację po elementach kolekcji za pomocą iteratora.

Znane implementacje interfejsu `Collection`

- Listy: ArrayList, LinkedList itp. Reprezentują uporządkowaną kolekcję, w której dopuszczalne są duplikaty.
- Zbiory: HashSet, TreeSet itp. Reprezentują nieuporządkowaną kolekcję unikalnych elementów.
- Kolejki: ArrayDeque, PriorityQueue itp. Służą do przechowywania elementów w kolejności, w której mają być przetwarzane.

