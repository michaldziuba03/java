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

Typy złożone zaś to wszelkie obiekty, tablice, klasy zdefiniowane przez użytkownika. Wbudowany String również jest typem złożonym.

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
