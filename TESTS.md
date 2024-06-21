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
