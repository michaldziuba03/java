## Typowe interakcje z bazą danych SQLite w Javie

### Stworzenie wrappera na połączenia

> SQLConnection.java

```java
package org.example;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class SQLConnection {
    private Connection conn;

    public void connect(String dbname) {
        try {
            conn = DriverManager.getConnection("jdbc:sqlite:" + dbname);
            System.out.println("Connected");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void disconnect() {
        try {
            conn.close();
            System.out.println("Disconnected");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public Connection getConnection() {
        return conn;
    }
}
```

### Stworzenie klasy która zarządza danym zasobem w kontekście bazy danych

> User.java

Rekord (może też być klasa jak chcemy) który reprezentuje zasób - przykładowo użytkownika

```java
package org.example;

public record User(int id, String name, String password) {}
```

Przykładowa klasa wykonująca CRUD (Create - Read - Update - Delete)

> UserRepository.java

```java
package org.example;
import java.sql.*;

public class UserRepository {
    Connection conn;

    UserRepository(SQLConnection db) {
        conn = db.getConnection();
    }

    void init() throws SQLException {
        Statement statement = conn.createStatement();
        // bezpieczne do odpalania wiele razy, bo użyłem IF NOT EXISTS podczas tworzenia tabeli
        statement.executeUpdate(
                "CREATE TABLE IF NOT EXISTS users (" +
                        "id INTEGER PRIMARY KEY," +
                        "username VARCHAR(250) UNIQUE," +
                        "password VARCHAR(1024)" +
                    ")"
        );
    }

    int create(String username, String password) throws SQLException {
        String query = "INSERT INTO users (username, password) VALUES (?, ?)";
        PreparedStatement statement = conn.prepareStatement(query);
        statement.setString(1, username);
        statement.setString(2, password);
        statement.executeUpdate();

        ResultSet results = statement.getGeneratedKeys();

        if (!results.next()) {
            throw new RuntimeException("Something went wrong :*");
        }

        return results.getInt(1);
    }

    User findById(int id) throws SQLException {
        String query = "SELECT * FROM users WHERE id = ?";
        PreparedStatement statement = conn.prepareStatement(query);
        statement.setInt(1, id);

        ResultSet result = statement.executeQuery();
        if (result.next()) {
            String username = result.getString("username");
            String password = result.getString("password");

            return new User(id, username, password);
        }

        return null;
    }

    void deleteById(int id) throws SQLException {
        String query = "DELETE FROM users WHERE id = ?";
        PreparedStatement statement = conn.prepareStatement(query);
        statement.setInt(1, id);
        statement.executeUpdate();
    }
}
```

### Main

```java
package org.example;

import java.sql.SQLException;

public class Main {
    public static void main(String[] args) throws SQLException {
        SQLConnection db = new SQLConnection();
        db.connect("sample.db");

        UserRepository userRepository = new UserRepository(db);

        userRepository.init();
        userRepository.create("michaldziuba03", "topsecret");
    }
}
```
