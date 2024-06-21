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
