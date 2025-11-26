- Sealed interface - *restricts which classes can implement it.*
```
public sealed interface Result permits Pass, Fail { }
```
- Prevents unexpected implementations.
- Makes pattern matching (switch) exhaustive and safer.
- Ensures that only allowed subtypes (e.g., Pass and Fail) can represent validation outcomes.
- Does supplier have to be unique as Category?
- Delete button should only appear if: `!repo.hasProducts(supplierId)` -> Cannot delete a supplier if products reference them.
- SuppliersView must add double-click support -> TableView sets `setOnMouseClicked(event -> ...)`
- Validation does not save anything; it simply reports if the data is acceptable.
- The controller then decides what to do:
- If valid → save to repository
- If invalid → return to the form with error messages
- Repositories handle low-level database operations(Insert, Update, Delete, Read)
  
