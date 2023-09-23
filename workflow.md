# Workflow

This portfolio entry provides an overview of my understanding and application of workflow tools on GitHub. Specifically, it covers how I managed and executed tasks throughout their lifecycle:

* Accepting tasks from the project backlog
* Updating task information as required
* Progressing tasks on the task board
* Carrying out development on a feature branch
* Committing changes with meaningful comments
* Ensuring that work aligns with the Definition of Done (DoD)
* Creating pull requests

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






