## Dodanie Spring Boota przez Maven

### Wygenerowanie projektu od zera

Najłatwiej jest wygenerować po prostu projekt używając: https://start.spring.io/

> Można to zrobić od razu z poziomu IDE

![image](https://github.com/michaldziuba03/java/assets/43048524/621312e9-45b6-4bdb-a7ae-ee01a51e0cf8)


### Dodawanie manualnie do istniejącego projektu

Dodaj znacznik `<parent>` nad `<groupId>` twojego projektu:
```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

Zależności:

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

i plugin do znacznika `<plugins>` które znajdują sie w znaczniku `<build>`:

```xml
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
```


## Utworzenie najprostszej aplikacji

Potrzebujemy klasy która odpala nam aplikacje Springową:

```java
package web;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class WebApp {
    public static void main(String[] args) {
        SpringApplication.run(WebApp.class, args);
    }
}
```

i kontrolera który obsługuje ścieżki.

Każda metoda klasy z adnotacją `GetMapping` reprezentuje ścieżkę i jest jej handlerem.

Spring jest w stanie sie domyślić co zwrócić z takiego `RestControlelra`:
- klasy, recordy i mapy zwraca jako JSON
- string traktuje jako HTML albo text (przeglądarka wyrenderuje HTMLa)

```java
package web;

import org.example.User;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;

@RestController
public class UserController {
    @GetMapping("/json")
    User getJSON() {
        return new User(100, "michaldziuba03", "topsecret");
    }

    @GetMapping("/json2")
    Map getJSON2() {
        Map map = new HashMap<>();

        map.put("id", 200);
        map.put("username", "michaldziuba03");
        map.put("email", "mail@michaldziuba.dev");

        return map;
    }

    @GetMapping( "/html")
    String getHTML() {
        return "<h1>Hello World</h1>";
    }
}
```

Domyślnie Spring odpala aplikacje na porcie `8080`, więc wbijamy na `http://localhost:8080` + ścieżka z poziomu przeglądarki.
