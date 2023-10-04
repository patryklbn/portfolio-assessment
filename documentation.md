# Documentation 

This portfolio entry focuses on clean code principles and the use of Doxygen to generate documentation. I will briefly introduce a few key aspects of clean code principles and then apply them to refine the code I wrote in week 3. Next, I will generate project documentation using Doxygen. Finally, I will highlight examples from my code where I have eliminated the need for comments by adhering to the principles of clean code.

# Clean code

One of the most important aspects of software development is writing clean code. It promotes clear documentation and is crucial for ensuring others can understand our code.

### Clean code rules

A few key aspects of clean code are:


**Meaningful Names** - Consistently naming variables or functions is vital for readability. Effective naming enhances clarity and provides self-documenting code, making it easier to understand the code's purpose.

**Attention to Formatting** - Consistency in code formatting is essential for readability. Investing time in consistent formatting ensures that developers can easily locate specific sections of the code in the future.

**Comments** - While comments can be useful, they are often needed due to ambiguity or inconsistency in the code. By adhering to clear naming conventions for classes and methods and ensuring consistent formatting, the need for additional comments is reduced.

***DRY - Don't repeat yourself*** - Methods that frequently appear in the code performing the same function, or copied code snippets relocated to different parts of the project, can lead to potential errors. It's important not to replicate code needlessly.

***KISS - Keep it Simple, Stupid*** - Sticking to simplicity increases code clarity and eliminates the risk of error. Simpler code is not only more readable but also reduces the chance of errors.

***Single Responsibility Principle*** -  NEED TO ADD !!!!!!

### Clean code in practice

#### Original code

```
public class ContinentDatabase
{
    // SQLite connection for database acces
    private readonly SQLiteConnection db;

    // Path where the database file is located
    private readonly string databasePath;
    public const string DatabaseFilename = "ContinentDatabase.db3";

    public const SQLiteOpenFlags Flags =
        SQLiteOpenFlags.ReadWrite |
        SQLiteOpenFlags.Create |
        SQLiteOpenFlags.SharedCache;

    public ContinentDatabase(string DatabasePath)
    {
        databasePath = Path.Combine(DatabasePath, DatabaseFilename);
        Console.WriteLine("Initializing database connection...");
        db = new SQLiteConnection(databasePath, Flags);
        db.CreateTable<Continent>();
        Console.WriteLine("Continent table is set up.");
    }
```
#### Improved code

```
public class ContinentDatabase
{
    private readonly SQLiteConnection db;
    private readonly string _databasePath;
    public const string DatabaseFilename = "ContinentDatabase.db3";
    public const SQLiteOpenFlags Flags = SQLiteOpenFlags.ReadWrite | SQLiteOpenFlags.Create | SQLiteOpenFlags.SharedCache;

    public ContinentDatabase(string databasePath)
    {
        _databasePath = Path.Combine(databasePath, DatabaseFilename);

        Console.WriteLine("Initializing database connection...");
        db = new SQLiteConnection(_databasePath, Flags);

        CreateTable();
    }
    private void CreateTable()
    {
        Console.WriteLine("Setting up Continent table...");
        db.CreateTable<Continent>();
    }
```

In the improved version of the code, I introduced a `CreateTable` method to encapsulate the table creation logic. This separates the responsibility of table creation from the primary task of the `ContinentDatabase`, which is to initialize the database. By doing this, I've adhered to the ***Single Responsibility Principle*** ensuring that each method has a single task. Additionally, the revised code is more self-descriptive, allowing for the removal of unnecessary comments.

# Documentation 

