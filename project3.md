# Project 3

In this portfolio entry, I'll share the progress and insights from the third week of the team project. I'll provide examples of the code I wrote and explain the testing process. I'll also reflect on my experience reviewing both my own code and a teammate's. Lastly, I will offer reflections to illustrate how I'm continuing to grow as a software engineer

Contents

## Issue 

This week, I've picked up an issue and assigned it to myself. Unlike last week, since we've moved our Kanban board from Zube.io to GitHub, I've moved my card from 'To Do' to 'In Progress' in the Project section. The [issue](https://github.com/timh1975/UNDAC-Project/issues/15) that I've picked up this week is:

<details><summary>As an UNDAC Deputy Team Leader I want to view the status of all team IT so that I can take appropriate action in case of failure, temporary unavailability or reduction in service
</summary>

**End user goal:**
To have an overview of all IT systems and services

**End business goal:**
To ensure the smooth running of the mission

**Acceptance criteria:**

* IT systems can be listed with their current status
* Available an unavailable systems are clearly differentiated

**Notes:**

* The status of systems and services is assumed to be updated automatically by monitoring
  software.

</details>

This week, I focused on improving the UNDAC App by adding a feature for the Deputy Team Leader to monitor all IT systems and services. This update is designed to facilitate quick action in response to IT issues, ensuring minimal downtime and efficient mission operation. The new feature allows IT systems to be listed with their current status, with clear indicators for availability. 

### Implementation

Since the issue was relatively simple and very similar to that of the previous week, I've focused on improving the architecture and structure while writing the feature. I've followed the MVVM pattern to enhance the separation of the UI logic, presentation logic, and business logic. In addition, I've introduced a data layer for the creation of the database, and for retrieving and storing data. Following this pattern and enhancing the separation of these components makes them easier to maintain, test, or make further changes.

First, I started with my Model that holds data. The `ITSystemModel` class contains necessary attributes for my task, such as ID, Name, and Availability. I also introduced an event handler that occurs when a property is changed.

Next, I've implemented the data layer. The `ITSystemDatabase` class, along with its interface `IITSystemDB`, manages the creation of an SQLite database and table, inserts sample data, and provides basic operations such as retrieving all data from the database. The service class `ITSystemServices`, using the interface `IITSystemServices`, accepts the `IITSystemDB` interface through its constructor for database access. It implements operations to retrieve all data as a list, fetch data by ID, and initialize the database with table creation and data seeding. Introducing interfaces for both of these classes allows me to separate concerns: the service class manages database operations without being tightly coupled to it. This means if I want to change the database configuration, I can do so without needing to refactor the service class. The ITSystemServices class also implements an interface that enables access from different layers, further enhancing modularity and testing in isolation. 

```
    /// <summary>
    /// Interface for services managing IT system data.
    /// </summary>
    public interface IITSystemServices
    {
        Task<IEnumerable<ITSystemModel>> GetAllITSystemsAsync();
        Task InitializeDatabaseAsync();
    }
    /// <summary>
    /// Services for managing IT system data.
    /// </summary>
    public class ITSystemServices : IITSystemServices
    {
        /// <summary>
        /// The database interface 
        /// </summary>
        private readonly IITSystemsDB _itSystemsDatabase;

        /// <summary>
        /// Constructor for the IT system services with the database access.
        /// </summary>
        /// <param name="itSystemsDatabase">The database interface for IT system data</param>
        public ITSystemServices(IITSystemsDB itSystemsDatabase)
        {
            _itSystemsDatabase = itSystemsDatabase;
        }
```


The following phase was the ViewModel. The `ITSystemViewModel` class accesses services by accepting the `IITSystemServices` interface through its constructor. The ViewModel instantiates an `ObservableCollection` to display IT Systems, which is bound to the XAML of the page. The class also initializes the database, loads IT systems data, and populates the `ObservableCollection` with it.

Lastly, an instance of `ITSystemViewModel` is passed to the view represented by `ITSystemPage` when it's created. This is done by passing the `ViewModel` as a parameter via the constructor and assigning it to the private field. The `BindingContext` of the `ITSystemPage` is then set to this instance of `ITSystemViewModel`, thereby connecting the ViewModel to the View. The class contains only one method that is called when the page is about to appear, which triggers the initialization of the ViewModel.

```
    /// <summary>
    /// ViewModel for the IT systems
    /// </summary>
    public class ITSystemViewModel
    {
        /// <summary>
        /// Service for accessing IT system data
        /// </summary>
        private readonly IITSystemServices _itSystemServices;

        /// <summary>`
        /// Collection of IT systems to be viewed.
        /// </summary>
        public ObservableCollection<ITSystemModel> ITSystems { get; set; }

        /// <summary>
        /// Constructor for the ITSystemViewModel with IT system services.
        /// </summary>
        /// <param name="itSystemServices">Service for IT system data access.</param>
        public ITSystemViewModel(IITSystemServices itSystemServices)
        {
            _itSystemServices = itSystemServices;
            ITSystems = new ObservableCollection<ITSystemModel>();
        }
```

In the end, I registered services with the dependency injection container in the MauiProgram file.

Implementing my feature following the MVVM pattern adheres to several fundamental software engineering principles and best practices, such as:

**Separation of concerns**: This involves dividing the feature into distinct sections that each handle specific tasks like UI logic, presentation logic, business logic, and data access logic. This minimizes the dependencies between parts of the application, making it easier to manage and evolve.

**Interface Segregation Principle (ISP)**: A SOLID principle that is applied by creating specific interfaces for different clients, such as IITSystemDB and IITSystemServices, rather than one general-purpose interface. This prevents classes from depending on interfaces they do not use.

**Dependency Inversion Principle (DIP)**: Another SOLID principle that is implemented when interfaces (IITSystemDB, IITSystemServices) are used to define how classes interact with each other, rather than having classes depend directly on concrete implementations.

**Dependency Injection**: Injecting dependencies provides better modularity and facilitates testing, as dependencies can be mocked or replaced as needed.

These practices contribute to a more robust, maintainable, and testable codebase.

### Testing

Separating even more layers than last week allows me to test more components of my feature. Passing an interface of the database class to the service class allows me to test it without relying on a live database. The introduction of the ViewModel and passing the interface of the service class to it allow me to test the functionality of the ViewModel in isolation and without dependency on the service class. This completely separates the UI layer, reduces the complexity of the UI, and consequently reduces the need for extensive testing the UI.

The `GetAllITSystems_ReturnsCorrectData` test checks if the `ITSystemServices` class gets all IT System entries from a mock database correctly. It uses sample data and compares the number of items returned by the method to what was expected.

```
	public class TestITSystemServices
	{
        [Fact]
        public async Task GetAllITSystems_ReturnsCorrectData()
        {
            // Arrange
            var testData = new List<ITSystemModel>
            {
                new ITSystemModel { Id = 1, Name = "Test System 1", IsAvailable = true },
                new ITSystemModel { Id = 2, Name = "Test System 2", IsAvailable = false }
            };
            var mockDb = new Mock<IITSystemsDB>();
            mockDb.Setup(db => db.GetAllITSystemsFromDB()).ReturnsAsync(testData);

            var service = new ITSystemServices(mockDb.Object);

            // Act
            var result = await service.GetAllITSystemsAsync();

            // Assert
            Assert.NotNull(result);
            var resultList = result.ToList();
            Assert.Equal(testData.Count, resultList.Count);
        }
    }
}
```

The `LoadITSystems_ReturnObservableCollection` test makes sure that the `ITSystemViewModel` loads IT Systems into its collection properly by using a mock service. It verifies that the ViewModel’s collection has the correct number of loaded items.


```
	public class TestITSystemViewModel
	{
            [Fact]
            public async Task LoadITSystems_ReturnObservableCollection()
            {
                // Arrange
                var testData = new List<ITSystemModel>
                    {
                        new ITSystemModel { Id = 1, Name = "Test System 1", IsAvailable = true },
                        new ITSystemModel { Id = 2, Name = "Test System 2", IsAvailable = false }
                    };
                var mockService = new Mock<IITSystemServices>();
                mockService.Setup(service => service.GetAllITSystemsAsync()).ReturnsAsync(testData);

                var viewModel = new ITSystemViewModel(mockService.Object);

                // Act
                await viewModel.LoadITSystems();

                // Assert
                Assert.NotNull(viewModel.ITSystems);
                Assert.Equal(testData.Count, viewModel.ITSystems.Count);
            }
        }
```

Both tests work without the need for a real database or service, which means they are more reliable and not influenced by outside issues like database problems.



