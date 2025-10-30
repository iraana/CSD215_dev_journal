# Layers of data storage
## 1. In-memory storage (temporary):

   While your program runs, you use a data structure like an `ArrayList` to hold your list of tasks.
      - This is fast and easy to modify.
      - But it disappears when the program ends — it lives only in RAM.
## 2. Persistent storage (permanent):

   To make the list survive between runs, you save (or “serialize”) it to a file on the disk — something like `tasks.txt  .
      - When the app starts, you read that file and rebuild your in-memory list.
      - When the app exits or when you change the list, you write the current list back to the file.
