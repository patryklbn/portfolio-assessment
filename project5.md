# Project work 5

This is the final portfolio entry of my group project. I will present the good software practices that I have developed throughout this project. I'll provide examples of code that demonstrate these good practices, discuss the unit tests I wrote this week, and describe the review processes in which I participated, including both reviewing my own work and that of a teammate. Finally, I'll discuss my improvement as a software engineer, highlight the new challenges I faced, suggest possible improvements, and offer an overall conclusion.

###### Contents
*  [Issue](https://github.com/patryklbn/portfolio-assessment/blob/master/project5.md#issue)
*  [Testing](https://github.com/patryklbn/portfolio-assessment/blob/master/project5.md#testing)
*  [Code Reviews](https://github.com/patryklbn/portfolio-assessment/blob/master/project5.md#code-reviews)
*  [Reflection](https://github.com/patryklbn/portfolio-assessment/blob/master/project5.md#reflection)

## Issue

This week, I've assign myself to the new task. Following my personal workflow I started from picking a [task from Issues](https://github.com/timh1975/UNDAC-Project/issues/73) on GitHub, assign it to myself and moved card on Kanban board to appropiete column from "ToDo" to "In Progress". My task for this week:

**As an UNDAC Analyst, I want to view the status of current and completed operations so that I can evaluate the effectiveness of the mission**

This week I focus on enhancing the UNDAC App by incorporating a feature tailored for the UNDAC Analyst. This feature is designed to provide a comprehensive overview of all operational statuses, encompassing both ongoing and completed missions. The goal is to empower the Analyst with the necessary tools to effectively assess the impact and efficiency of each mission. This new functionality will offer a detailed listing of operations, complete with their respective statuses.

### Implementation

I began my implementation in a manner similar to previous weeks, by creating a branch using the team's agreed-upon naming convention. To maintain my personal workflow, I started by fetching updates from the remote repository. Since there were no merges to the Development branch last week, I didn't need to merge the Development branch with my local feature branch.

Since the implementation of this feature is very similar to those I've done in the past weeks, I focused on developing this task by following all the improvements I made throughout this project. The feature aligns with the MVVM pattern, separating different layers to make the code more modular and less dependent. My final enhancement in this pattern is the introduction of a repository class, `OperationStatusRepository`, which is responsible for bridging between my database and the service layer, thus isolating the service class from direct database operations. The repository class includes database operations such as initializing the database by creating tables and seeding operation status data, as well as methods to add or update data in the database.

`OperationStatusRepository` implements the interface `IOperationStatusRepository`, which is passed to the service class using dependency injection, with the interface being passed through the constructor. Similarly, the service class `OperationStatusServices` implements the interface `IOperationStatusService`s, which is passed to the `OperationStatusViewModel` connected to View class `OperationStatusPage` which binding data from the ViewModel. 

Demonstrating my good software engineering practices, I will discuss my repository class:

```
/// <summary>
    /// Interface for repository operations 
    /// </summary>
    public interface IOperationStatusRepository
    {
        Task<IEnumerable<OperationStatusModel>> GetAllAsync();
        Task<OperationStatusModel> GetByIdAsync(int operationId);
        Task AddNewAsync(OperationStatusModel operationStatus);
        Task UpdateAsync(OperationStatusModel operationStatus);
        Task InitializeDatabaseAsync();
    }

    /// <summary>
    /// Repository for operation status management in the database
    /// </summary>
    public class OperationStatusRepository : IOperationStatusRepository
    {
        private readonly ITSystemContext _context;


        /// <summary>
        /// Constructor for the repository.
        /// </summary>
        /// <param name="context">Database context for operation statuses</param>
        public OperationStatusRepository(ITSystemContext context)
        {
            _context = context;
        }


        /// <summary>
        /// Retrieves all operation statuses from the database.
        /// </summary>
        /// <returns>An enumerable collection of OperationStatusModel.</returns>
        public async Task<IEnumerable<OperationStatusModel>> GetAllAsync()
        {
            return await _context.OperationStatuses.ToListAsync();
        }

        /// <summary>
        /// Retrieves an operation status by its ID.
        /// </summary>
        /// <param name="operationId">ID for the operation status.</param>
        /// <returns>Specified OperationStatusModel.</returns>
        public async Task<OperationStatusModel> GetByIdAsync(int operationId)
        {
            return await _context.OperationStatuses
                .FirstOrDefaultAsync(os => os.Id == operationId);
        }

        /// <summary>
        /// Adds a new operation status to the database.
        /// </summary>
        /// <param name="operationStatus">The operation status to add.</param>
        public async Task AddNewAsync(OperationStatusModel operationStatus)
        {
            await _context.OperationStatuses.AddAsync(operationStatus);
            await _context.SaveChangesAsync();
        }

        /// <summary>
        /// Updates an existing operation status in the database.
        /// </summary>
        /// <param name="operationStatus">The operation status with updated information.</param>
        public async Task UpdateAsync(OperationStatusModel operationStatus)
        {
            _context.OperationStatuses.Update(operationStatus);
            await _context.SaveChangesAsync();
        }
        /// <summary>
        /// Initializes the database by creating tables and seeding operation status data.
        /// </summary>
        public async Task InitializeDatabaseAsync()
        {
            try
            {
                await _context.Database.EnsureCreatedAsync();

                if (!await _context.OperationStatuses.AnyAsync())
                {
                    var sampleData = new List<OperationStatusModel>
                    {
                        new OperationStatusModel { Id = 1, OperationName = "Rescue Operation", StartDate = new DateTime(2023, 1, 1), EndDate = new DateTime(2023, 1, 7), Status = "Completed", Description = "Rescue operation in mountains" },
                        new OperationStatusModel { Id = 2, OperationName = "Relief Distribution", StartDate = new DateTime(2023, 2, 1), Status = "In Progress", Description = "Distributing relief materials post-flood." }
                    };

                    await _context.OperationStatuses.AddRangeAsync(sampleData);
                    await _context.SaveChangesAsync();
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error in InitializeDatabaseAsync: {ex.Message}");
                throw;
            }
        }
```
<br>
<br>

Code demonstrates several good software practices, including principles of SOLID and clean code.

**SOLID Principles:**
- Single Responsibility Principle (SOLID): Each method in the OperationStatusRepository class has a single responsibility. For instance, GetAllAsync retrieves all operation statuses, while AddNewAsync adds a new status. This separation ensures that each method performs one task.

- Open/Closed Principle (SOLID): The use of an interface `IOperationStatusRepository` for the repository class allows the class to be extended without modifying it. New functionalities can be added by creating new implementations of the interface without changing the existing implementation.

- Dependency Inversion Principle (SOLID): The repository class depends on abstractions `ITSystemContext` rather than concrete implementations. This reduces coupling between the data layer and the business logic.
<br>
<br>

**Clean Code Principles:**

- Code Readability: The method names like GetAllAsync, AddNewAsync are self-explanatory, describing their functionality which makes the code more readable.

- Use of Doxygen Comments: The use of Doxygen comments for methods and classes enhances understanding and helps in maintaining the code.

- Error Handling: The InitializeDatabaseAsync method includes error handling to catch exception, ensuring the program's stability.

- Asynchronous Programming: The use of async and await ensures that the database operations are performed asynchronously.


By following these principles, code not only adheres to the SOLID principles but also enhances the readability and maintainability.

### Testing

Introducing the repository again changed my approach to conducting unit tests. Since the service class is now isolated from the database and uses the repository interface, I've entirely reverted to mocking. To test the service class, I've mocked the repository interface, and for testing the ViewModel, I've used the service class interface.


For the `OperationStatusServicesTests`, the setup phase involves creating a mock `IOperationStatusRepository` object, _mockRepository to replicate data layer interactions. Next, an instance of `OperationStatusServices`, _operationStatusServices, is instantiated using the mock repository, nsuring a environment for testing the service behavior.


```
public class OperationStatusServicesTests
{
    private readonly Mock<IOperationStatusRepository> _mockRepository;
    private readonly OperationStatusServices _operationStatusServices;

    public OperationStatusServicesTests()
    {
        _mockRepository = new Mock<IOperationStatusRepository>();
        _operationStatusServices = new OperationStatusServices(_mockRepository.Object);
    }
```
<br>
<br>

This test ensures that the `OperationStatusServices` class correctly retrieves a specific operation status by its ID. By using a mocked `IOperationStatusRepository`, the test simulates the retiving of a sample operation status from the repository, verifying that the service returns the correct data.
```
    // Test for retrieving an operation status by its ID
    [Fact]
    public async Task GetOperationStatusByIdAsync_ReturnsCorrectStatus()
    {
        var sampleStatus = new OperationStatusModel
        {
            Id = 1,
            OperationName = "Sample Operation",
            StartDate = new DateTime(2023, 1, 1),
            Status = "Completed",
            Description = "Sample Description"
        };

        _mockRepository.Setup(repo => repo.GetByIdAsync(1)).ReturnsAsync(sampleStatus);

        var result = await _operationStatusServices.GetOperationStatusById(1);

        Assert.Equal(sampleStatus.Id, result.Id);
        Assert.Equal(sampleStatus.OperationName, result.OperationName);
    }
```
<br>
<br>
This test checks if the `OperationStatusServices` class successfully adds a new operation status to the database. It uses a mock repository to simulate the addition of a new operation status and then verifies that the repository's AddNewAsync method was called, ensuring the service interacts correctly with the data layer.

```
    // Test for adding a new operation status
    [Fact]
    public async Task AddOperationStatusAsync_AddsStatusSuccessfully()
    {
        var newStatus = new OperationStatusModel
        {
            OperationName = "Sample Operation",
            StartDate = new DateTime(2023, 1, 10),
            Status = "In Progress",
            Description = "Sample Description"
        };

        _mockRepository.Setup(repo => repo.AddNewAsync(newStatus)).Returns(Task.CompletedTask).Verifiable();

        await _operationStatusServices.AddOperationStatus(newStatus);

        _mockRepository.Verify();
    }

```
<br>
<br>

In the setup phase for `OperationStatusViewModelTests`, a mock `IOperationStatusServices` object _mockService is initialized to simulate service layer interactions. Next, an `OperationStatusViewModel` instance, _viewModel is created with this mock service, ensuring a environment for testing the ViewModel's behavior.

```
public class OperationStatusViewModelTests
{
    private readonly Mock<IOperationStatusServices> _mockService;
    private readonly OperationStatusViewModel _viewModel;

    public OperationStatusViewModelTests()
    {
        _mockService = new Mock<IOperationStatusServices>();
        _viewModel = new OperationStatusViewModel(_mockService.Object);
    }
```
<br>
<br>

This test checks if the  `OperationStatusViewModel` correctly loads operation statuses into its collection. The test involves setting up mock data, invoking the LoadOperationStatuses method, and then asserting that the ViewModel's collection not only contains the correct number of items but also includes specific operation statuses, validating if the ViewModel handle and display operation status correctly.
```
    // Test for loading operation statuses into the ViewModel
    [Fact]
    public async Task LoadOperationStatusesAsync_LoadsStatusesIntoViewModel()
    {
        var mockData = new List<OperationStatusModel>
    {
        new OperationStatusModel
        {
            Id = 1,
            OperationName = "Operation A",
            StartDate = new DateTime(2023, 1, 1),
            Status = "Completed",
            Description = "Description A"
        },
        new OperationStatusModel
        {
            Id = 2,
            OperationName = "Operation B",
            StartDate = new DateTime(2023, 2, 1),
            Status = "In Progress",
            Description = "Description B"
        }
    };

        _mockService.Setup(service => service.GetAllOperationStatusesAsync()).ReturnsAsync(mockData);

        await _viewModel.LoadOperationStatuses();

        Assert.Equal(mockData.Count, _viewModel.OperationStatuses.Count);
        Assert.Contains(_viewModel.OperationStatuses, item => item.OperationName == "Operation A");
        Assert.Contains(_viewModel.OperationStatuses, item => item.OperationName == "Operation B");
    }
```
<br>
<br>
After all my tests passed, I committed my changes and performed a sanity check to ensure that there were no items in my code, such as 'ToDo' comments, that shouldn't be pushed to the remote repository. Then, I pushed my branch and opened a pull request.

## Code Reviews

### Reviewing My Code 

Feedback on my [pull request](https://github.com/timh1975/UNDAC-Project/pull/125) was provided in the comments, along with a screenshot indicating the requested changes. A team member pointed out that the code is clean, readable, and follows software principles. My colleague also noticed my improvement compared to last week, highlighting minor code formatting issues and the lack of Doxygen comments. I made the requested changes; I fixed the formatting and added Doxygen comments, then committed and pushed my feature branch.

Then, I responded in the comments that the changes were made, resolved the conversation, and merged my branch with the Development branch. Following the newly added step in our workflow, I deleted my feature branch after merging.

I think the code review provided this week by my team member was professional, tactful, and maintained a friendly and encouraging tone. The requested changes were well-spotted and made sense.

### Reviewing Someone Else's Code

I reviewed the [pull request](https://github.com/timh1975/UNDAC-Project/pull/124) of the same person who reviewed mine. My feedback was provided in a general comment and in code comments, along with an example of the suggested change. I mentioned the good structure of the code, adherence to good coding practices, and the use of Doxygen comments. One suggestion I made was to add exception handling to the method initializing the database for better robustness and early detection of potential database issues. In the code comment, I provided an example of how these changes could be implemented.

I tried to maintain a professional but friendly tone in my feedback. I first highlighted the positive aspects of the pull request and then suggested improvements for system stability.

Since this pull request did not have a negative impact on the codebase, I approved it. Afterward, I noticed that my team member committed the suggested changes and merged their branch.

## Reflections

