# Project work 1
This portfolio entry will demonstrate my ability to integrate the various software engineering practices that I have learned so far in this module. The next three portfolio entries will be based on the group project developing the UN Disaster Assessment and Coordination (UNDAC) App.   
An App is built in the .NET framework as the development environment, with C# as the main programming language, and cross-platform compatibility using the .NET Multi-platform App UI (MAUI) framework.
A team was provided a set of documented user stories and an entity relationship diagram, and the app would use an SQLite instance for all database operations.

###### Contents
*  [Issue](https://github.com/patryklbn/portfolio-assessment/blob/master/project1.md#issue)
*  [Testing](https://github.com/patryklbn/portfolio-assessment/blob/master/project1.md#testing)
*  [Code Reviews](https://github.com/patryklbn/portfolio-assessment/blob/master/project1.md#code-reviews)
*  [Reflection](https://github.com/patryklbn/portfolio-assessment/blob/master/project1.md#reflection)

## Issue

All user stories were transferred to the GitHub issues on our [team's repository](https://github.com/timh1975/UNDAC-Project) and on our zube.io kanban board. Every team member was free to select the issue that they wanted to work on and assign it to themselves. The [issue](https://github.com/timh1975/UNDAC-Project/issues/9) that I assigned to myself this week was:
<details><summary>As an UNDAC Deputy Team Leader I want to view a list of all local media agencies so that I can contact them immediately
</summary>

**End user goal:**
To be able to send out information as efficiently as possible

**End business goal:**
To ensure that the mission has an appropriate channel of communication to the local population

**Acceptance criteria:**

* Media agencies can be listed
* The list can be filtered by media type (e.g. 'press', 'radio', tv', etc.)
* List filters can be cleared
* Contact details for a particular agency can be readily viewed

</details>

In this task, I developed a feature for the UNDAC App allowing Deputy Team Leaders to swiftly access a list of local media agencies, crucial for outreach during missions. The functionality includes listing media agencies, filtering them by media type ("press", "radio", "tv"), clearing the filters, and providing a user-friendly display of contact details.
This feature is key to ensuring that vital information is promptly disseminated to the local population through the most effective channels.

### Implementation 

To implement my feature, I created an SQLite database environment, `mediaDB`, to handle operations specific to my feature, fetching paths and flags from the `Constants` class, which was created by another team member. The model MediaAgencies is responsible for representing entities within the Media Agencies feature. Meanwhile, `MediaAgenciesPage` manages UI events and interactions, such as displaying a list of media agencies, handling user actions like selecting a media type, and invoking methods to load or refresh data.

While writing the code, I strived to employ good software design practices. Here are some examples from my code that demonstrate adherence to these principles.

In the snippet of code presented below, I adhered to the **Single Responsibility Principle (SRP)** and used **meaningful names**, reducing the need for unnecessary **comments**. Each method serves a distinct purpose, enhancing the code's maintainability and clarity for others. I have provided comments at the top of each method for integration with Doxygen documentation, ensuring that other developers can understand the purpose and usage without delving into the specific implementations.

```
        /// <summary>
        /// Create local database and table if it does not exist
        /// </summary>
        public async Task InitiateDataBase()
        {
            if (_connection == null)
            {
                _connection = new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
            }
            var result = await _connection.CreateTableAsync<MediaAgencies>();
        }

        /// <summary>
        /// Inserts sample data into the database 
        /// </summary>
        public async Task InsertSampleData()
        {
            await InitiateDataBase();
            var count = await _connection.Table<MediaAgencies>().CountAsync();
            if (count > 0)
                return;

            var sampleData = new List<MediaAgencies>
            {
                new MediaAgencies { Name = "Agency 1", MediaType = "press", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 2", MediaType = "radio", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 3", MediaType = "tv", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 4", MediaType = "press", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 5", MediaType = "radio", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 6", MediaType = "tv", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 7", MediaType = "press", ContactDetails = "contact@gmail.com" },
                new MediaAgencies { Name = "Agency 8", MediaType = "radio", ContactDetails = "contact@gmail.com" },             };
        

            await _connection.InsertAllAsync(sampleData);
        }

        /// <summary>
        /// Retrieves all media agencies from the database
        /// </summary>
        /// <returns>List of media agencies</returns>
        public async Task<List<MediaAgencies>> GetAllMediaAgenciesFromDB()
        {
            await InitiateDataBase();
            return await _connection.Table<MediaAgencies>().ToListAsync();
        }
```

In another snippet of code below, we can observe several software engineering principles. Apart from good practices that also apply in this example mentioned earlier, such as the **Single Responsibility Principle (SRP)** by designing methods for specific tasks and **meaningful names**, a clear naming convention results in self-explanatory code and eliminates the need for **comments**.

Furthermore, this snippet adheres to **DRY (Don't Repeat Yourself)** by separating the operations of inserting sample data and loading media agencies into different methods. This approach avoids redundancy, making the codebase more adaptable to future changes. It also follows the **KISS (Keep It Simple, Stupid)** principle, as the simplicity of methods, without any unnecessary complexity, not only makes the code more understandable but also reduces potential points of failure.

```
        /// <summary>
        /// Inserts sample data and loads it into the media agencies list
        /// </summary>
        private async Task InsertAndLoadSampleDataAsync()
        {
            await _db.InsertSampleData();
            await LoadMediaAgenciesAsync();
        }

        /// <summary>
        /// Loads all media agencies from the database
        /// </summary>
        private async Task LoadMediaAgenciesAsync()
        {
            _allAgencies = await _db.GetAllMediaAgenciesFromDB();
            mediaAgenciesList.ItemsSource = _allAgencies;
        }
```


By sticking to these principles, I enhanced the robustness, readability, and maintainability of the software, making it easier for others to collaborate, update, and debug the code.

My feature was developed as planned, functioning fully and meeting the acceptance criteria set by the issue. After ensuring everything was working, I committed my changes, pushed my feature branch, and opened a [pull request](https://github.com/timh1975/UNDAC-Project/pull/42).

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/33.png?raw=true" alt="UNDAC">
  <figcaption><b>Fig.1 - UNDAC App - Implemented feature</b></figcaption>
</figure>

## Testing 

During the testing phase of my feature, I focused on the database and its operations. Tests were written using the xUnit framework, which referenced the main project. I began my testing by setting up a test database environment and then performed three tests to check if the operations were working correctly.

`TestInitiateDataBase` verified whether the database is initialized correctly. First, it called a method to initiate the test database, then retrieved all media agencies from the database, and finally checked if the list is not null, confirming that data was inserted into the database.

```
        public async void TestInitiateDataBase()
        {
            // Call method to initiate database
            await _db.InitiateDataBase();

            // Retrieves all media agencies from the database
            var allAgencies = await _db.GetAllMediaAgenciesFromDB();

            // Check if allAgencies list should is initialized
            Assert.NotNull(allAgencies); 
        }
```

        
`TestInsertSampleData` verified if sample data is inserted into the database properly. It began by calling a method to insert sample data into the database, then retrieved all media agencies from the database, and finally, it checked if the list is not empty, ensuring that sample data was inserted correctly.


```
        [Fact]
        public async void TestInsertSampleData()
        {
            // Call method to insert sample data into the database
            await _db.InsertSampleData();

            var allAgencies = await _db.GetAllMediaAgenciesFromDB();

            // Check if allAgencies list is not empty
            Assert.True(allAgencies.Count > 0); 
        }
```

`TestGetAllMediaAgenciesFromDB` ensured that all media agencies inserted in the previous test are retrieved from the database correctly. It did so by calling a method to retrieve all media agencies from the database and then checking if the list is not empty, confirming that the database initialized in previous tests still exists and contains the inserted sample data.

```
        [Fact]
        public async void TestGetAllMediaAgenciesFromDB()
        {
            var allAgencies = await _db.GetAllMediaAgenciesFromDB();

            // Check if allAgencies list is not empty
            Assert.True(allAgencies.Count > 0);
        }
```
<br>
<br>
<br>
<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/30.png?raw=true" alt="UNDAC">
  <figcaption><b>Fig.2 - Tests - All tests passed</b></figcaption>
</figure>


## Code Reviews 

Code reviews turned out to be quite challenging due to reliance on others in my group to contribute. Only a few people were active most of the time, engaging in the code reviews, and I faced the risk of being left without the opportunity to perform any code reviews. I contacted another student from a different group for code reviews, and I sent him my zipped code for review. In the meantime, one person from my group opened a PR, which allowed me to review his code. Additionally, I received feedback from a member of another team.

### Reviewing My Code

The reviewed code came with two suggestions to improve my work. The first was related to naming conventions, suggesting a change in my class name from `mediaDB` to `MediaDB`, in line with the C# convention that class names should always start with an uppercase letter. This was something I overlooked while developing my feature, and it was quite an obvious mistake. To resolve this issue, I've renamed my class to `MediaDB`, changed all occurrences of 'mediaDB' in the code to 'MediaDB', cleaned the project, and restored it.

The second suggestion I received was about improving how my database was initialized. Initially, I was calling the 'InitiateDataBase' method in every other method performing operations on the database, which wasn't efficient. I realised this approach could indeed make my code vulnerable because if, by any chance, the initialization method wasn't called, it could lead to failures during runtime.

Recognizing this valid point, I decided to refine my approach to make my code more robust. After some research, I came up with a solution that ensures the database is always initialized before any operations are performed. Here's what I implemented:

```
        public static async Task<MediaDB> CreateDatabase(string databasePath)
        {
            var database = new MediaDB(databasePath);
            await database.InitiateDataBase(); 
            return database;
        }
```

With this static method, I can create and initialize the database effectively, removing the need to call `InitiateDataBase()` in every operational method. This change makes my code more efficient and less prone to errors, as the database initialization is guaranteed. 

### Reviewing Someone Else's Code

In this section, I reviewed a team member's code, adding comments to his [pull request](https://github.com/timh1975/UNDAC-Project/pull/48). I proposed three suggestions on how my team member's code could be improved.

First, I recommended refactoring the code to store objects in a list instead of hardcoding each object. This strategy could facilitate further development, as all objects could be accessed from the list, eliminating the need to initialize them again.

My second suggestion concerned the naming convention. I pointed out that parameter names should follow the camelCase format.

Lastly, I advised adding descriptive comments to sections of the code where their purpose wasn't clear, enhancing readability and maintainability for others who might work on the project in the future.

## Reflection

### Difficulties and Learning Outcomes

This week, while working on my issue, I encountered several difficulties.

After the initial development phase of my feature, I pushed my feature branch to the repository for the first time. Surprisingly, it was pushed to the master branch. Initially, I was quite confused because, in Visual Studio, I was clearly working on my feature branch. Even during the commit process, the IDE confirmed I was on the correct branch. Upon further investigation, I discovered that my branch was set to upstream to the master. As a result, when I pushed my branch, it went straight to the master. Fortunately, another team member came to my rescue, restoring the master branch to the state of the previous commit. I then recreate my feature branch, this time ensuring there were no upstreams set.

Before this incident, I was unfamiliar with upstream, which taught me an important lesson: I need to pay close attention to every detail when pushing anything to a remote repository. Another takeaway is the good practice of pushing my branch to the remote right after its creation, before making any changes.

Another challenge I faced was getting the Unit Tests to work. Firstly, I encountered a problem that everyone has in week 6 during the test battles. I struggled to reference my main project in the unit tests, as they were targeting different frameworks. My main project is a multi-platform MAUI project with multiple targets, and the xUnit test project uses the net7.0 framework. The troubleshooting page provided in week 6 didn't exactly resolve my issue. When I attempted to add the net7.0 framework to the main project, I found I couldn't use the emulator to run the device.

I devised a solution to append net7.0 at the end of the list of target frameworks. This adjustment allowed me to use the emulator and set up project references from my unit tests to the main project.

```
	<PropertyGroup>
		<TargetFrameworks>net7.0-android;net7.0-ios;net7.0-maccatalyst;net7.0</TargetFrameworks>
		<TargetFrameworks Condition="$([MSBuild]::IsOSPlatform('windows'))">$(TargetFrameworks);net7.0-windows10.0.19041.0</TargetFrameworks>
		<!-- Uncomment to also build the tizen app. You will need to install tizen by following this: https://github.com/Samsung/Tizen.NET -->
		<!-- <TargetFrameworks>$(TargetFrameworks);net7.0-tizen</TargetFrameworks> -->
		<OutputType Condition="'$(TargetFramework)' != 'net7.0'">Exe</OutputType>
```

Writing tests also came with its own problems. I really struggled to mock the database and perform unit tests, especially since my logic was implemented in the MediaAgenciesPage, which directly handles the UI. It's a point I need think more about it since I didn't find a way to mock the database effectively, and I think next time, I will try to separate the business logic from the presentation layer to make testing more straightforward.

I didn't want to skip the testing phase entirely, so I decided to shift my focus and perform a few simple integration tests instead of unit tests to check the functionality of the database. While this wasn't what I initially had in mind it verified the database's operations.

### Conclusion

While working on the issue this week, I feel I spent too much time on it. I got stuck a few times and spent hours finding a solution. Generally, I believe this task provided me with good learning outcomes, and I think working on the issue next week will be much easier. Working as a group was challenging at the beginning, as not many people were active during the week, but later on, I think our teamwork improved significantly. More people became involved and actively supported each other. During this week, I've learned many important things, not only about coding but also about version control, code reviews, and teamwork. I'm confident that our collaborative work next week will be more efficient.




