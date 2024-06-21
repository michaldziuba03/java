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

## Dynamiczne ścieżki

```java
package web;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
    @GetMapping("/{name}")
    String hello(@PathVariable("name") String name) {
        return "Hello, " + name;
    }
}
```

![image](https://github.com/michaldziuba03/java/assets/43048524/9365ea21-b5a5-4b18-b2cd-4d88ab17f698)

## Metody HTTP z ciałem (POST, PUT itd)

Mam klasę która reprezentuje formularz rejestracji.

BARDZO WAŻNE - klasa musi mieć gettery i settery bo inaczej Spring nie będzie w stanie przemapować JSONa na naszą klasę: 

```java
package web;

public class RegisterDto {
    String email;
    String password;

    public boolean validate() {
        if (email == null || password == null) {
            return false;
        }

        if (email.length() < 2) {
            return false;
        }

        return password.length() >= 8;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

Przykład kontrolera z metodą `@PostMapping` która używa z `@RequestBody`.

> jako bonus też dałem wyrzucanie wbudowanych w Springa wyjątków które zwracają odpowiedni status HTTP (Bad Request - 400).

```java
package web;

import org.apache.coyote.BadRequestException;
import org.springframework.web.bind.annotation.*;

@RestController
public class UserController {
    @PostMapping("/register")
    RegisterDto register(@RequestBody RegisterDto body) throws BadRequestException {
        System.out.println(body);
        if (!body.validate()) {
            throw new BadRequestException("Email or password too short");
        }

        return body;
    }
}
```
