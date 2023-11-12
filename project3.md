# Project work 3

In this portfolio entry, I'll share the progress and insights from the third week of the team project. I'll provide examples of the code I wrote and explain the testing process. I'll also reflect on my experience reviewing both my own code and a teammate's. Lastly, I will offer reflections to illustrate how I'm continuing to grow as a software engineer

###### Contents
*  [Issue](https://github.com/patryklbn/portfolio-assessment/blob/master/project3.md#issue)
*  [Testing](https://github.com/patryklbn/portfolio-assessment/blob/master/project3.md#testing)
*  [Code Reviews](https://github.com/patryklbn/portfolio-assessment/blob/master/project3.md#code-reviews)
*  [Reflection](https://github.com/patryklbn/portfolio-assessment/blob/master/project3.md#reflection)

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

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/43.jpg?raw=true" alt="Feature">
  <figcaption><b>Fig.1 - UNDAC App - Implemented feature</b></figcaption>
</figure>

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

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/44.jpg?raw=true" alt="Tests">
  <figcaption><b>Fig.2 - Tests - All tests passed</b></figcaption>
</figure>


## Code Reviews

The code review process went smoothly for me this week. After few hours of opening my PR, one of the team member reviewd my code and I the same one the same day. 

### Reviewing My Code 

Feedback on my [pull request](https://github.com/timh1975/UNDAC-Project/pull/102) was provided through comments on two classes. The first comment on my Model class highlighted the effective use of an interface and the implementation of Doxygen comments. The second comment advised paying attention to the comments I make when committing changes to the Development branch. After opening the PR, I realized I had deleted some comments from the XAML file during a subsequent commit. I've since double-checked my code and comments to ensure all are necessary and clear.

Although not specifically mentioned in the review, I noticed that despite having Doxygen comments, I hadn't generated any output. To address this, I first created a Doxyfile by navigating to my project directory in the terminal and running the command `doxygen -g Doxyfile`. Then, I created a Documentation folder in my solution. Next, I configured the Doxyfile by opening it in a text editor, setting the project name, and specifying the path to the project root directory. After running the command `doxygen Doxyfile`, I checked the produced HTML file, which correctly displayed output from my in code comments.

This review process, while not directly related to the initial feedback, prompted me to reevaluate my comments and led to the realisation that I had forgotten to set up the output for Doxygen.

While I was setting up my Doxygen output, another team member merged his feature branch, which included the Doxyfile. Therefore, I decided not to push my Doxyfile and output to the shared repository to avoid conflicts.

After my feature branch was approved and merged into the Development branch, aligning with our team's workflow, I moved my issue card to Completed on the Kanban board and closed [the issue](https://github.com/timh1975/UNDAC-Project/issues/15).

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/48.jpg?raw=true" alt="Code Review">
  <figcaption><b>Fig.3 - Screenshot from GitHub showcasing review of my code and generated Doxygen documentation</b></figcaption>
</figure>


### Reviewing Someone Else's Code

This week, a team member asked me to review a specific class implementing ViewModel. He was still working on the issue and wanted to merge his [pull request](https://github.com/timh1975/UNDAC-Project/pull/103) into his test branch.

In review i left two comments, one general comment discribing good overall structure of the code, and good implementation od data binding and advise to consider to add more doxygen comments. In second comment Ive provided feedback to the specific class which i was asked to review. 

I advised my colleague to further separate the ViewModel class by introducing a service class and moving the data loading method logic there. I also suggested implementing an interface for the service class and injecting it into the ViewModel to avoid direct model instantiation. This solution could help achieve better separation of the presentation layer from the business layer and facilitate easier testing by allowing both the service class and ViewModel to be tested using mocked interfaces.

I think my suggestion could help my team member implement the MVVM pattern more effectively, which he was attempting to do. Therefore, since his original code was well-structured and he wanted to merge it into his test feature, I approved the PR and the branch was merged.

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/46.jpg?raw=true" alt="Code Review">
  <figcaption><b>Fig.4 - Screenshot from GitHub showing a general comment as well as a specific comment on a class during a code review</b></figcaption>
</figure>


## Reflection

### Improvments 

This week, my software engineering practice improved by enhancing the architecture while working on a new feature branch. Last week, I worked to align with a three-layer architecture, which led to a better understanding of the structure of the application, a better grasp of interfaces and dependency injection, and consequently, better isolation of each component and improved testing using a mocking framework. This week, I've implemented my feature following the MVVM pattern, which resulted in even better isolation. Each component is separate and doesn't rely on others, and the data layer, business layer, logic layer, and presentation layer are isolated, making the code more robust and easier to test. Introducing more interfaces has allowed me to test not only the database services but also the ViewModel, and further changes or enhancements in a particular layer don't affect the others.

Team workflow improved this week by merging feature branches into the Development branch only after the feature branches were reviewed. This approach helped prevent unexpected merges into the Development plan, and by the end, I think everyone started to understand the process and team workflow better. Team members followed the agreed-upon branch naming convention and used the Kanban board appropriately.

### Difficulties 

While working on my issue, I encountered a problem with registering dependencies in the dependency container in the MauiProgram class. My registrations couldn't be resolved when I tried to use interfaces in the `MainPage` while adding an event handler to navigate to my page. After some investigation, it turned out to be a common problem when using the MAUI framework, and the solution was to introduce a helper class.

Creating a static helper class `ServiceHelper` and initiating its object in the event handler method, with the interface to my service class passed as a parameter, resolved my problem. I was then able to retrieve services from the Dependency Injection container.

```
/// <summary>
/// Provides helper methods to retrieve services from the Dependency Injection containter 
/// </summary>

public static class ServiceHelper
{
    public static TService GetService<TService>()
        => Current.GetService<TService>();

    public static IServiceProvider Current =>
#if WINDOWS10_0_17763_0_OR_GREATER
			MauiWinUIApplication.Current.Services;
#elif ANDROID
            MauiApplication.Current.Services;
#elif IOS || MACCATALYST
			MauiUIApplicationDelegate.Current.Services;
#else
			null;
#endif
}
```

This week, our team encountered a problem with merging feature branches that had been approved. Many team members faced various issues while working on their tasks, leading to numerous changes in the main project files. This resulted in many conflicts in the Development branch. One team member took on the task of resolving these conflicts and merging the feature branches into the Development branch. I believe this is a common problem in team development situations, and delegating one person to resolve conflicts is a good approach, ensuring that all conflicts are resolved using a consistent methodology.

### Further Possible Improvements
There are a couple of improvements we could make in the future to our team's workflow. After three weeks of working on tasks, our team has generated a large number of feature branches, making our repository a bit messy. In line with the GitHub workflow, I think it would be beneficial if everyone deleted their feature branch after it had been merged into the Development branch.

Another improvement could be addressed in documentation. I chose not to add my Doxyfile to the shared repository since another team member had already pushed it. However, when I open the Doxygen configuration file, it sets a local path. Perhaps we could all share a Doxyfile configured only for the project directory and with the same project name, while keeping the generated documentation locally. This would avoid conflicts with files from the output and also allow us to generate all project documentation locally. Fetching all changes from the development branch and then generating output will ensure the documentation includes all features of the entire project.

### Conclusion
Within three weeks of collaborative work on the team project, I believe we have improved significantly as a team, and I have personally grown as a software engineer. After encountering a few issues at the beginning, such as with the code review process, the subsequent weeks went much more smoothly, and our workflow became better understood by everyone.

The past three weeks taught me a lot about good software practices, including maintaining well-structured, documented, and clean code. I now have a better understanding of how the GitHub process works and have started performing operations directly in the terminal rather than in the IDE. I've also gained more insight into interfaces, dependency injection, how to separate different components of the application, and how to use mocking for unit testing.

Overall, I'm pleased with the progress we've made as a team and the personal growth I've experienced. I believe the last few weeks have been particularly beneficial for me.
