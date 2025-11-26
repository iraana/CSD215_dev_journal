13.11.2025
# Project Structure, Code Overview
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
- ViewModel (MVVM) pattern: the view gets all data and actions from a separate container (ViewModel) so the UI just displays info and triggers callbacks.
- Each view has a pure function that takes a ViewModel and returns a JavaFX Node; the view just builds the UI from the data without handling logic.
- Uses separate types for Unvalidated vs. Validated data so the compiler forces validation.
- Even if both types have the same fields, they’re different types, so you can’t save unvalidated data by mistake.
- This pattern uses the type system to prevent errors before the program even runs.
- The helpers package provides useful tools:

`NumberField` → a JavaFX input field that only accepts numbers.

`createColumn(...)` → shortcut for adding table columns without repeating boilerplate code.

`setOnDoubleClick(...)` → connects table row double-clicks to an action from the ViewModel.

Design

<img width="819" height="600" alt="image" src="https://github.com/user-attachments/assets/4dbe2193-05c1-486a-941a-bed5a046b3b9" />

---

26.11.25
- If onDelete null, we don't show the button in the edit form.
- Category category in Product record for referencing category of product.
- then add helper function in record to get supplier name
- in product repository update query to have Supplier/Category object.
- In ProductEditView we need to add select Supplier
- Add list of Categories/Suppliers for choosing ( need id, name, list)
- Create choiceBox -> example in EditView.
- 
