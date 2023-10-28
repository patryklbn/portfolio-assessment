# Project work 1
This portfolio entry will demonstrate my ability to integrate the various dimensions of software engineering into practice that I have learned so far in this module. The next three portfolio entries will be based on the group project developing the UN Disaster Assessment and Coordination (UNDAC) App.   
An App is built in the .NET framework as the development environment, with C# as the main programming language, and cross-platform compatibility using the .NET Multi-platform App UI (MAUI) framework.
A team was provided a set of documented user stories and an entity relationship diagram, and the app would use an SQLite instance for all database operations.

In each weak my portfolio will include:
* A detailed summary of the issue I tackled.
* Snippets from my code, complemented by commentary illustrating my adherence to good software design practices.
* An explanatory overview of the test code I've written.
* A reflective analysis of the changes requested in the code reviews, along with the adjustments I've made.
* An insightful summary of any issues I've discovered while reviewing others' code.
* A reflective segment pinpointing specific learnings, challenges, or observations I've encountered along the way.

## Issue

All user stories were transferred to the GitHub issues on our team's repository and on our zube.io kanban board. Every team member was free to select the issue that they wanted to work on and assign it to themselves. The issue that I assigned to myself this week was:
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

In another snippet of code below, we can observe several software engineering principles. Apart from good practices that also apply in this example mentioned earlier, such as the **Single Responsibility Principle (SRP)** by designing methods for specific tasks and meaningful names, a clear naming convention results in self-explanatory code and eliminates the need for comments.

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

My feature were developed as planned, fully working and meeting acceptace criteria from the issue.
### Testing 








