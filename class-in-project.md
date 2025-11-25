13.11.2025
# Project Structure
- Uses *JDBC (SQLite)* for database access.
- Uses *JavaFX* for GUI.
- List of modules : https://docs.oracle.com/en/java/javase/25/docs/api/index.html
- Modoles have packages inside
- WARNING: Use --enable-native-access=javafx.graphics to avoid a warning for callers in this module
- WARNING: Use --enable-native-access=org.xerial.sqlitejdbc to avoid a warning for callers in this module
- Fix those warnings in edit configuration -> modify options -> add VM options
- Repositories create a a barrier between my application and the database.
- The only place where SQL code appears is inside of code inside my data package.
- Delegate the work of figuring out what the SQL is to the repository and that way my main application code is not tied to a specific version of SQL or even a specific database.
- The data service is just the the place where we get the initial connection object.
- Singleton means a design pattern where only one instance of a class exists in the entire application.
