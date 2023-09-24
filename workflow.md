# Workflow

This portfolio entry provides an overview of my understanding and application of workflow tools on GitHub. Specifically, it covers how I managed and executed tasks throughout their lifecycle:

* Accepting tasks from the project backlog
* Updating task information as required
* Progressing tasks on the task board
* Carrying out development on a feature branch
* Committing changes with meaningful comments
* Ensuring that work aligns with the Definition of Done (DoD)
* Creating pull requests

 ###### Contents
*  [Accepting the task](https://github.com/patryklbn/portfolio-assessment/blob/master/workflow.md#accepting-the-task)
*  [Development](https://github.com/patryklbn/portfolio-assessment/blob/master/workflow.md#development)
*  [Unit Testing](https://github.com/patryklbn/portfolio-assessment/blob/master/workflow.md#unit-testing)
*  [Finalizing the Development Process](https://github.com/patryklbn/portfolio-assessment/blob/master/workflow.md#finalizing-the-development-process)
*  [Reflection](https://github.com/patryklbn/portfolio-assessment/blob/master/workflow.md#reflection)

The specific issue tackled involved creating a local database using SQLite, implementing CRUD functionality, and ensuring its reliability through unit tests.

## Task workflow

### Accepting the task

In deciding on our workflow, our team opted to manage our tasks using a Kanban board on zube.io, which is seamlessly integrated with our group's GitHub repository. One of our team members took the initiative to import the provided issues into our Kanban board as a backlog. We collectively decided that team members could independently choose issues from the backlog. Upon selecting an issue, I opened the card, assigned myself to it, and shifted the card's status on the board to In Progress (Fig.1). Subsequently, I verified that I was also assigned to this [issue on GitHub](https://github.com/timh1975/UNDAC-App-Project/issues/5 "Issue 5"). 

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/15.png?raw=true" alt="Kanban board">
  <figcaption><b>Fig.1 - zube.io - Assigned task card and kanban board</b></figcaption>
</figure>

### Development

#### Creating a branch
I began my development process by creating a new feature branch in our group repository. This approach ensures the separation of my work from the master branch, minimizing the potential for conflicts with my teammates' contributions. As a team, we agreed upon a naming convention: feature/feature_name-name-surname-ID-taskID. To establish the new branch, I executed the following steps:

> 1. Opened the terminal.
> 2. Navigated to the directory containing my cloned shared repository.
> 3. Created and immediately checked into the new feature branch using the command:

```git checkout -b <feature/continents-patryk-gronczewski-ID-5>```

Next, I dove into the core of my task. I initiated my work within Visual Studio, using Solution Explorer to create a separate library specifically for the local database with its associated CRUD functionality. My decision to do this was informed by the need to segregate the database from the multi-platform MAUI application. This precaution minimizes potential framework conflicts and enhances overall stability. Here are the steps I took:

> 1. Right-clicked on the solution.
> 2. Selected Add > New Project... > Class library.
> 3. After setting up the library, I proceeded to add the SQLite packages. I did this using the NuGet manager:
>> 4. Right-clicked on Dependencies within the newly created project.
>> 5. Choose Manage NuGet Packages.
>> 6. Installed the necessary SQLite packages, as illustrated in Fig.2.

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/16.png?raw=true" alt="NuGet manager">
  <figcaption><b>Fig.2 - NuGet manager - Adding neccessary SQLite packages</b></figcaption>
</figure>

#### Data Access Class

I started by linking the main application, **UNDAC**, to my library. This allows both to work under the same namespace. Here’s how I achieved this:

> 1. Right-clicked on Dependencies under the main project.
> 2. Navigated to > Add Project References...
> 3. Selected my library and confirmed the selection. (Fig. 3).

Following this, I created the `Continent.cs` data access class. It serves as the backbone for my database, holding the essential data. As specified in the issue, I defined the table name as "continents" and set up a getter and setter for the single column as a **Primary Key** in my database- "name". (Fig.4)

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/17.png?raw=true" alt="References and class">
  <figcaption><b>Fig.3 and Fig.4 - Project references and Continent class - Adding project references and creating Continent class</b></figcaption>
</figure>

#### Configuring App Constants

I followed a [Microsoft tutorial](https://learn.microsoft.com/en-us/dotnet/maui/data-cloud/database-sqlite "Microsoft tutorial"). to lay the foundation for my database configuration.

First, I established the database name and path.
Subsequently, I defined the SQLite flags: `ReadWrite`, `Create`, and `SharedCache`.
I then wrote a method to initiate the database connection, setting its full path. For improved log handling, I integrated some console outputs to monitor the process. (Fig.5)

#### Implementing CRUD Functionality

With the core setup in place, I integrated **CRUD** operations into the `ContinentDatabase` class. I wrote methods for essential data manipulations: **Create**, **Read**, **Update**, and **Delete**. Additionally, to enhance database utility, I incorporated a method that fetches a continent by its name using **Linq**.(Fig.6)

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/18.png?raw=true" alt="App Constants and CRUD configurations">
  <figcaption><b>Fig.5 and Fig.6 - App Constants and CRUD - Configuring App Constants and implementing CRUD functionality</b></figcaption>
</figure>

### Unit Testing

A measure of the success of my issue is: "Unit tests pass for all CRUD operations." Therefore, I embarked on the process of implementing comprehensive unit tests..

To begin, I created a new project within my solution, opting for the **NUnit Test project** under the **Tests** option. Just like I did with my library, I added the necessary SQLite packages to handle SQLite functionalities. To facilitate the tests, I incorporated a dependency to my "Continent" library, granting seamless access.

The cornerstone of my testing process starts with setting up a database instance specifically tailored for testing. From there, I initialize a temporary database solely for the purpose of these tests. 

```
   public class Tests
    {

        // The database instance for the tests
        private ContinentDatabase _db;

        [SetUp]
        public void Setup()
        {
            // Initializes a temporary directory for testing the database
            var tempDirectory = Path.Combine(Path.GetTempPath(), Guid.NewGuid().ToString());

            Directory.CreateDirectory(tempDirectory);

            _db = new ContinentDatabase(tempDirectory);
        }
```

Subsequently, I create methods to rigorously test the CRUD operations: Create, Read, Update, and Delete. Each test was designed to ensure the robustness and accuracy of the database functionalities. 

```
        [Test]
        public void AddContinentTest()
        {
            // Initial count of continnts
            var firstCount = _db.GetContinents().Count();

            // Add the test continent to the database
            var newContinent = new Continent { name = "Europe" };

            // Add the test continent to the database
            _db.AddContinent(newContinent);

            // Ensure the continent count increased by one
            var secondCount = _db.GetContinents().Count();
            Assert.AreEqual(firstCount + 1, secondCount);
        }

        [Test]
        public void GetContinentTest()
        {
            // New continent for testing
            var newContinent = new Continent { name = "Australia" };

            // Add the test continent to the database
            _db.AddContinent(newContinent);

            // Ensure the retrieved continent matches the test continent
            var retrievedContinent = _db.GetContinents().FirstOrDefault(c => c.name == "Australia");
            Assert.IsNotNull(retrievedContinent);
            Assert.AreEqual("Australia", retrievedContinent.name);
        }

        [Test]
        public void UpdateContinentTest()
        {
            // New continent for testing
            var newContinent = new Continent { name = "Asia" };
            _db.AddContinent(newContinent);

            // Updated name for the test continent
            var updatedName = "Europe";

            // Add the first test continent to the datebase
            _db.UpdateContinent(newContinent);

            // Update name of the continent
            newContinent.name = updatedName;

            // Add the continent with the new name to the database
            _db.AddContinent(newContinent);

            // Ensure the retrieved continent's name matches the updated name
            var retrievedContinent = _db.GetContinents().FirstOrDefault(c => c.name == updatedName);
            Assert.IsNotNull(retrievedContinent);
            Assert.AreEqual(updatedName, retrievedContinent.name);
        }

        [Test]
        public void DeleteContinentTest()
        {
            // Add a test continent to the database
            var testContinentName = "Africa";
            var testContinent = new Continent { name = testContinentName };
            _db.AddContinent(testContinent);

            // Ensure the continent was added
            var addedContinent = _db.GetContinent(testContinentName);
            Assert.IsNotNull(addedContinent, "Continent was not added successfully");

            // Delete the test continent from the database
            _db.DeleteContinent(testContinentName);

            // Ensure the continent was deleted
            var deletedContinent = _db.GetContinent(testContinentName);
            Assert.IsNull(deletedContinent, "Continent was not deleted successfully.");
        }
```

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/19.png?raw=true" alt="Unit Tests">
  <figcaption><b>Fig.7 - Unit Tests - Successfully passed unit tests.</b>b></figcaption>
</figure>

### Finalizing the Development Process

#### Definition of Done (DoD)

Upon completion of my task, I checked my work against the Definition of Done (DoD) before committing changes. My DoD consists of the following:

* All acceptance criteria are met
* Functional tests passed
* No known defects
  
After ensuring that my work aligns with the defined criteria, I was ready to commit my changes.

#### Committing changes

Committing changes is an essential part of version control. It means saving our work to the local repository. It's beneficial to commit frequently, especially after completing a feature or resolving a bug. This way, we capture small changes and make things clear for anyone reviewing.

After I finished my feature, I broke it down into three separate commits. Each one has a clear message that says what I did. This keeps things organized.

To commit, I used the terminal. I went to my project's folder and typed in:

```
git add path/tofile
git commit -m "brief description"
```
<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/20.png?raw=true" alt="GitHub">
  <figcaption><b>Fig.8 - GitHub - Commit messages</b></figcaption>
</figure>

#### Pull request 

After committing my changes, I initiated a [pull request](https://github.com/timh1975/UNDAC-App-Project/pull/25 "Pull request") on our group's GitHub repository. I included comments detailing my accomplishments and the modifications I made. After ensuring everything aligned with our group's workflow, I merged my feature branch into the development branch.(Fig.9)

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/21.png?raw=true" alt="GitHub">
  <figcaption><b>Fig.9 - GitHub - Pull request with comment</b></figcaption>
</figure>
<br>
<br>
However, post-merging, I realised that some crucial files, like the library and unit tests, were absent. For a detailed account of this challenge, you can refer to [pull request](https://github.com/timh1975/UNDAC-App-Project/pull/25 "Pull request") . To address this oversight, I promptly added the missing files, verified that everything was correctly positioned, and then created [a new pull request](https://github.com/timh1975/UNDAC-App-Project/pull/30). I made sure to leave a descriptive comment explaining the essence of the additional pull request.(Fig.10)

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/23.png?raw=true" alt="GitHub">
  <figcaption><b>Fig.10 - GitHub - A new pull request with comment</b></figcaption>
</figure>
<br>
<br>
Although I chose to keep the feature branch for potential future reference, it's worth noting that, according to common GitHub workflows, feature branches are typically deleted after being merged into the main or development branch.

#### Update the task board 

After completing my issue, I navigated to our group's Kanban board and moved my task card from "In Progress" to "Done". This action automatically closed the [issue on GitHub](https://github.com/timh1975/UNDAC-App-Project/issues/5 "Issue 5") as well.(Fig.11)

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/22.png?raw=true" alt="Kanban board">
  <figcaption><b>Fig.11 - zube.io - Updated Kanban board</b></figcaption>
</figure>

# Reflection

#### Difficulties

I faced two main problems while working on this task:

**Unit Test Issues**: I had a tough time adding unit tests. My main app was built using MAUI, which uses a multi-platform framework. On the other hand, the NUnit tests needed the NET.6 or NET.7 frameworks. I couldn't get them to work together. So, I made a separate library for my local database and the access class. This way, both my main app and the unit tests could use this library without any problems.

**File Organisation**: Because of the first problem, I ended up placing the new library and unit test folders outside of the GitHub repository. This meant they were missing when I tried to commit and make a pull request. I tried moving these files, but it caused more problems. In the end, I had to back up my code, delete the wrongly placed projects, and add them back correctly. Then, I could add the missing library and unit test to my feature branch.

This second issue taught me to double-check everything. Our team's workflow is that the person making a pull request can also approve it. But, because of this issue, I realised it's better if someone else reviews it. That way, they might spot something you missed.

#### Improving the Process

I think our Definition of Done (DoD) could be better. One thing to add might be to check that the whole project is correctly lined up with the GitHub repository. As for our task board, it's been good so far. The Kanban board on zube.io works well with GitHub and is easy to use.

