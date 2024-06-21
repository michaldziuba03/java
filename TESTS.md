## Instalacja zależności

```xml
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter-api</artifactId>
  <version>5.10.2</version>
  <scope>test</scope>
</dependency>
```

### Najbardziej prosty test

Główna idea testowania: wywołać przykładowo jakąś funkcję która zazwyczaj coś zwraca i sprawdzić czy zwraca to czego sie spodziewamy.

Testy dodajemy do folderu `/src/tests/java` gdzie InteliJ IDEA sie domyśli, że dana klasa to test i pozwoli jednym przyciskiem je wykonać.

```java
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

public class TestTest {
    @Test
    public void obvious() {
        Assertions.assertEquals(1, 1);
    }
}
```

### Praktyczniejszy przykład

Powiedzmy, że mamy w naszej aplikacji taką klasę i chcemy ją wytestować:

```java
package org.example;

public class Post {
    private String title;

    public void setTitle(String title) {
        if (title.length() < 5) {
            throw new RuntimeException("Too short title");
        }

        this.title = title;
    }

    public String getTitle() {
        return title;
    }
}
```

Najczęściej będziemy chcieli czy funkcja zwraca coś/wyrzuca wyjątek (albo sprawdzić czy NIE zwraca/wyrzuca wyjątek używając wariantów metod z `not` w nazwie).
By test był wiarygodny testujemy wiele scenariuszy - z podanymi błędnymi danymi jak i poprawnymi.

```java
import org.example.Post;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

public class PostTest {
    @Test
    void tooShortTitle() {
        Post post = new Post();
        Assertions.assertThrows(Throwable.class, () -> post.setTitle("123"));
    }

    @Test
    void validTitle() {
        Post post = new Post();
        Assertions.assertDoesNotThrow(() -> post.setTitle("123456"));
    }

    @Test
    void checkGetter() {
        Post post = new Post();
        String expected = "Hello World.";
        post.setTitle(expected);

        Assertions.assertEquals(post.getTitle(), expected);
        Assertions.assertEquals(post.getTitle().length(), expected.length());
    }
}
```
