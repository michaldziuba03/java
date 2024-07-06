
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
| **Przechowywanie w pamięci** | Stos                 | Sterta                            |
| **Przekazywanie do metod** | Przez wartość  (jest kopiowane)  | Przez referencję                  |
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

Klasa `Person` jest przekazywana jako referencja/wskaźnik do metody, więc wszelkie zmiany będą widoczne również w oryginalnej zmiennej.

String jest również przekazywany jako referencja ale jest on zachowuje sie w specyficzny sposób - gdy modyfikujemy Stringa, tak naprawdę tworzymy nowego Stringa, ponieważ String jest niemutowalny. Wyobraź sobie, że gdy przekazujesz Stringa, to kopiowana jest jego referencja, potem ta kopia referencji jest nadpisywana i wskazuje na nowo utworzony String `"Michał"`. Z tego powodu oryginalna zmienna pozostaje bez zmian.

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
