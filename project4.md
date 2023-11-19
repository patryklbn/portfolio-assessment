# Project work 4

In this portfolio entry, I'll share the progress and insights from the fourth week of the team project. This week, I continued my work on the group project, focusing on further developing features and enhancing the overall functionality. I'll provide examples of the code I've contributed this week, delve into the testing process I undertook, and reflect on my experiences in code review sessions, both reviewing my work and that of my teammates. Finally, I'll discuss my ongoing development as a software engineer, highlighting new challenges I faced and the strategies I employed to overcome them

###### Contents
*  [Issue](https://github.com/patryklbn/portfolio-assessment/blob/master/project4.md#issue)
*  [Testing](https://github.com/patryklbn/portfolio-assessment/blob/master/project4.md#testing)
*  [Code Reviews](https://github.com/patryklbn/portfolio-assessment/blob/master/project4.md#code-reviews)
*  [Reflection](https://github.com/patryklbn/portfolio-assessment/blob/master/project4.md#reflection)

## Issue 

This week, I chose to enhance an existing feature instead of starting a new one. My goal was to upgrade the [IT Systems](https://github.com/timh1975/UNDAC-Project/issues/15) feature by replacing the SQLite database with Microsoft Entity Framework. This change aimed at improving data management and further refining the application's architecture.

Alongside the database upgrade, I planned to enhance the separation of layers in the MVVM pattern, focusing on clarity and maintainability.

To kick off this task, I created a [new issue](https://github.com/timh1975/UNDAC-Project/issues/112) in our team's GitHub repository, clearly naming and describing the intended enhancements. I then moved this issue to the "In Progress" column on our [Kanban board](https://github.com/users/timh1975/projects/2/views/1), marking the start of my work on these improvements.

### Implementation

The first step in implementing my enhancements was to create a new branch. Adhering to our team's naming conventions, I named my branch `feature/patryk/issue-15-efcore-upgrade`, which clearly identifies the purpose of the branch: upgrading the IT Systems feature (Issue 15) to use Entity Framework Core (EF Core). This naming strategy not only maintains clarity but also helps in tracking the specific enhancements being made. 

With the new branch set up, I began the process of transitioning from SQLite to EF Core. This involved several key steps:

**Setting up EF Core**: I started by integrating EF Core into application. This involved adding the necessary NuGet packages and setting up the `ITSystemContext`. This class acts as a session with the database.

```
public class ITSystemContext : DbContext
{
    public DbSet<ITSystemModel> ITSystems { get; set; }

    public ITSystemContext(DbContextOptions<ITSystemContext> options)
        : base(options)
    {
    }
}
```
<br>
<br>

**Refactoring the Service Class**: I refactored the service class to use the `ITSystemContext` class, which abstracts database operations through an interface. I added two additional CRUD methods, `AddITSystem` and `UpdateITSystem`, and included them in the interface's contract.

Service class:


```
    /// <summary>
    /// Services for managing IT system data.
    /// </summary>
    public class ITSystemServices : IITSystemServices
    {
        private readonly ITSystemContext _itSystemsDatabase;

        /// <summary>
        /// Constructor for the IT system services with the database context.
        /// </summary>
        /// <param name="itSystemsDatabase">The database context for IT system data</param>
        public ITSystemServices(ITSystemContext itSystemsDatabase)
        {
            _itSystemsDatabase = itSystemsDatabase;
        }
```

Interface:
```
    /// <summary>
    /// Interface for services managing IT system data.
    /// </summary>
    public interface IITSystemServices
    {
        Task<IEnumerable<ITSystemModel>> GetAllITSystemsAsync();
        Task<List<ITSystemModel>> GetAllITSystems();
        Task<ITSystemModel> GetITSystemById(int systemId);
        Task AddITSystem(ITSystemModel system);
        Task UpdateITSystem(ITSystemModel system);
        Task InitializeDatabaseAsync();
    }
```
<br>

Lastly, I've change register in MauiProgram class to use DbContext

By injecting the `ITSystemContext` into `ITSystemServices`, I adhered to the **Dependency Injection** principle, enhancing the testability of the code. The code also adheres to the **KISS** principle keeping the code simple, It features **meaningful names**, and is documented with Doxygen comments.
<br>

**Refactoring the Model and ViewModel**: To enhance the separation in my MVVM pattern, I decided to move `INotifyPropertyChanged` interface, which notifies clients that a property value has changed, from the `ITSystemModel` class to a new `ITSystemViewModelItem` class nested within `ITSystemViewModel`. I decided to nest these classes, rather than creating a separate class, for better clarity and organisation.

New `ITSystemViewModelItem` class:

```
    /// <summary>
    /// ViewModel item for an IT system
    /// </summary>
    public class ITSystemViewModelItem : INotifyPropertyChanged
    {
        private readonly ITSystemModel _itSystemModel;

        public ITSystemViewModelItem(ITSystemModel model)
        {
            _itSystemModel = model;
        }

        public int Id => _itSystemModel.Id;

        public string Name
        {
            get => _itSystemModel.Name;
            set
            {
                if (_itSystemModel.Name != value)
                {
                    _itSystemModel.Name = value;
                    OnPropertyChanged();
                }
            }
        }

        public bool IsAvailable
        {
            get => _itSystemModel.IsAvailable;
            set
            {
                if (_itSystemModel.IsAvailable != value)
                {
                    _itSystemModel.IsAvailable = value;
                    OnPropertyChanged();
                    OnPropertyChanged(nameof(AvailabilityText));
                }
            }
        }

        public string AvailabilityText => IsAvailable ? "Available" : "Unavailable";

        public event PropertyChangedEventHandler PropertyChanged;

        protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
```
<br>

By moving `INotifyPropertyChanged`, I separated the UI logic from the business layer in my MVVM pattern. By implementing a new class nested within my ViewModel, I adhered to the Single Responsibility Principle, making each class responsible for a single functionality.

My old database class, along with its settings, was deleted. I've checked my code using static analysis for any hints from the IDE and then performed dynamic analysis. I've run the code to check if my feature behaves correctly.

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/49.jpg?raw=true" alt="Feature">
  <figcaption><b>Fig.1 - UNDAC App - Working feature after upgrading to EF Core framework</b></figcaption>
</figure>

### Testing

Refactoring my feature from the use of SQLite to EF Core changed my approach to unit testing the service class functionality. Instead of mocking the database, I used EF Core's UseInMemoryDatabase method to simulate a real database. First, I had to add the package `Microsoft.EntityFrameworkCore.InMemory`.

The `GetAllITSystems_ReturnsCorrectData` test checks if the `ITSystemServices` class correctly retrieves all IT System entries from an in-memory database. This test mimics the interaction with a real database, providing a more accurate the service class's behavior.

```
        [Fact]
        public async Task GetAllITSystems_ReturnsCorrectData()
        {
            // Arrange
            var options = new DbContextOptionsBuilder<ITSystemContext>()
                .UseInMemoryDatabase(databaseName: "TestDatabase")
                .Options;

            using (var context = new ITSystemContext(options))
            {
                context.ITSystems.AddRange(
                    new ITSystemModel { Id = 1, Name = "Test System 1", IsAvailable = true },
                    new ITSystemModel { Id = 2, Name = "Test System 2", IsAvailable = false }
                );
                context.SaveChanges();
            }

            using (var context = new ITSystemContext(options))
            {
                var service = new ITSystemServices(context);

                // Act
                var result = await service.GetAllITSystemsAsync();

                // Assert
                Assert.NotNull(result);
                var resultList = result.ToList();
                Assert.Equal(2, resultList.Count);
            }
        }
```
<br>
<br>

The `GetITSystemById_NonExistingId` test checks if the `ITSystemServices` class correctly returns null when an IT System is queried by a non existing ID. This test ensure the robustness of the service class in handling edge cases.
<br>

```
        [Fact]
        public async Task GetITSystemById_NonExistingId()
        {
            // Arrange
            var options = new DbContextOptionsBuilder<ITSystemContext>()
                .UseInMemoryDatabase(databaseName: "TestDb")
                .Options;

            using (var context = new ITSystemContext(options))
            {
                var service = new ITSystemServices(context);

                int nonExistingId = 999;

                // Act
                var result = await service.GetITSystemById(nonExistingId);

                // Assert
                Assert.Null(result);
            }
        }
```
<br>
<br>

The tests for my ViewModel remained the same as last week, as they are separated from the data layer and use `IITSystemServices` to mock the service class.

```
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
<br>
After all my tests passed, I committed my changes and performed a sanity check to ensure that there were no items in my code, such as 'ToDo' comments, that shouldn't be pushed to the remote repository. Then, I pushed my branch and opened a pull request.

<br>
<br>
<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/50.jpg?raw=true" alt="Tests">
  <figcaption><b>Fig.2 - Tests - All tests passed</b></figcaption>
</figure>

## Code Reviews

### Reviewing My Code 

Feedback on my [pull request](https://github.com/timh1975/UNDAC-Project/pull/115) was provided in the form of the comment and suggested changes. My team member first provided general feedback that my original implementation was good and aligned with the MVVM pattern. He suggested refactoring my newly introduced `ViewModelItem` class to enhance the separation between my ViewModel and Model. The adjustment suggested implementing a SetProperty method to simplify property setters, reduce code duplication, and make the ViewModel easier to maintain and extend. I must admit that I didn't fully understand the proposed changes and had to spend some time investigating. After investigation, I gained a better understanding of implementing the SetProperty method to reduce code duplication by setting property logic in the SetProperty method rather than in each property. 

Since the suggested change indicates only to my Name property, I decided not to commit this change and instead implemented it locally along with the IsAvailable property. 

My refactored properties:
```
        public string Name
        {
            get => _name;
            set
            {
                if (SetProperty(ref _name, value, nameof(Name)))
                {
                    _itSystemModel.Name = value; 
                }
            }
        }

        public bool IsAvailable
        {
            get => _isAvailable;
            set
            {
                if (SetProperty(ref _isAvailable, value, nameof(IsAvailable)))
                {
                    _itSystemModel.IsAvailable = value; 
                    OnPropertyChanged(nameof(AvailabilityText));
                }
            }
        }

        public string AvailabilityText => IsAvailable ? "Available" : "Unavailable";

        protected bool SetProperty<T>(ref T field, T value, string propertyName)
        {
            if (EqualityComparer<T>.Default.Equals(field, value)) return false;
            field = value;
            OnPropertyChanged(propertyName);
            return true;
        }
```

After verifying that everything was working, I committed the changes and pushed my feature branch. Next, I responded to the feedback with a comment that the proposed changes were valid and that I had applied and committed them.
<br>
<br>
<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/56.jpg?raw=true" alt="Code Review">
  <figcaption><b>Fig.3 - Screenshot from GitHub showcasing review of my code</b></figcaption>
</figure>

### Reviewing Someone Else's Code

I've provided feedback on my team member's [pull request](https://github.com/timh1975/UNDAC-Project/pull/117), both as a general comment and in a code comment. First, I gave overall feedback, noting the well-structured code and effective use of Doxygen comments. Since I am reviewing this team member's code for the second time, I noticed an improvement from their previous pull request, particularly their start in using Doxygen comments, which I believe is a good development in their workflow.

I also suggested further validation of properties to enhance the code's reliability and added in-code comments about redundant comments.

Since the code changes do not seem to have a negative impact on the codebase, I've approved the pull request.
<br>
<br>

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/53.jpg?raw=true" alt="Code Review">
  <figcaption><b>Fig.4 - Screenshot from GitHub showing a general comment as well as a specific in code comment during a code review</b></figcaption>
</figure>

<br>
<br>

## Reflection

### Improvments 

This week, my major improvement involved upgrading my existing feature from a relational SQLite database to an Object-Relational Mapping (ORM) framework, specifically Entity Framework Core. This transition is an enhancement in good practice, as it leverages the robustness of ORM for more efficient data handling and maintenance. By implementing Entity Framework Core, I've improved the data access layer, improving code readability and maintainability, and also allowed to test service class by imitating real database using `UseInMemoryDatabase` method.

My next enhancement focused on further segregating UI logic from the business layer in MVVM pattern implementation. By transitioning the `NotifyPropertyChanged` implementation from the Model to the ViewModel, I've achieved a more complete separation of UI components. This is important for maintaining the integrity of the code, as it ensures that UI elements remain isolated from the business logic, leading to more reliable and maintainable code.

Since a team member recently resolved conflicts and merged all previous feature branches into the Development branch, I started my work this week by creating a new branch and fetching the latest updates from the origin. I merged my branch with the updated Development branch. I believe it's important to regularly sync my local codebase with the Development branch to stay up-to-date with the team's progress and avoid potential conflicts in the future. This practice has been a valuable improvement to my personal routine.

In line with my recent suggestion to enhance our team workflow, I brought up the idea of deleting feature branches after they're merged. This was discussed with the team, and as a result, our workflow.md has been updated. The new step added is to delete the feature branch once it's merged with the development branch. I believe this will help us keep our repository more organised.

### Difficulties 

Transitioning from SQLite to EF Core brought up a major difficulty that I couldn't resolve. When attempting to migrate the database to keep it in sync with data model, I faced an error due to the project's multi-platform nature and multiple frameworks. This issue, I found, is a common problem for MAUI apps using EF Core. Considering the potential risk to the application's stability and the impact on my teammates' work, I decided not to proceed with the migration.

### Further Possible Improvements

Currently, each team member sets up a new local database for individual features, which I believe might not be the most efficient approach. Instead, we could consider sharing a single database, utilizing different service classes and accesses for various model classes. This kind of significant design decision ideally should be agreed upon and implemented at the beginning of the project. For educational purposes, and to explore this idea further, I plan to experiment with using my existing EF Core setup for another feature.

### Conclusion

The past week of working on our group project brought further improvements to our team's workflow and the quality of my code. All previously approved feature branches were merged into the Development branch, thanks to a team member who handled the conflicts. Our application, with its additional features, is working smoothly without crashes, which is a positive sign. Moreover, every pull request opened this week received a review. We also enhanced our workflow by adding a step to delete branches after merging, keeping the repository more organized.

For me, this week was quite experimental, as I aimed to enhance my feature using the EF Core framework. This improvment provided me with a better understanding of EF Core, its differences from SQLite, and the potential difficulties in its implementation. Additionally, I improved the separation of layers in my work to better align with the MVVM pattern. The feedback received during the PR review of my code offered excellent suggestions on how to achieve even better results.

I believe there is still room for improvement in our team workflow and in my ability to write quality code. However, I feel we are on the right track, and each week I gain a deeper understanding of good software practices and how to maintain both personal and team workflows.
