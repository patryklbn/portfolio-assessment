# Project work 2

In this portfolio entry, I continue with the team project and demonstrate how my software engineering practice is improving. I'll cover in this entry:

* Descriptive summary of the issue.
* Code snippets and commentary that highlight good software engineering practice.
* Descriptive summary of the tests.
* Reflective summary of code review changes.
* Descriptive summary of peer code review.
* General reflective section.

## Issue 

Similar to the previous week, everyone within the team was free to pick an issue and assign themselves to it. I picked an issue, assigned it to myself, and moved the card on the Kanban board from the backlog to the 'In Progress' section. The issue I've worked on:

<details><summary>As an UNDAC Team Support and Logistics Manager, I want to view the status (health, location, current deployments) of all team vehicles and other equipment so that I can respond to equipment requests
</summary>

**End user goal:**
To have an overview of mission vehicles in an easy-to-understand manner

**End business goal:**
Ensure that the mission has adequate vehicles for both support functions and operations

**Acceptance criteria:**

* Vehicles can be viewed in a list
* The vehicle list can be sorted and filtered on individual fields and by a free-text search
* Details of a single vehicle can be readily viewed

</details>

In this week's task, I enhanced the UNDAC App by implementing a feature that allows the Team Support and Logistics Manager to monitor the status of all team vehicles and other equipment efficiently. This functionality enables the viewing of vehicles in a list format, sorting and filtering the list based on type of the vehicle, and displaying detailed information for individual vehicles.

### Implementation

This week, I continued to build upon the foundation laid in the previous week. I created a SQL environment named `VehiclesDB` to manage operations related to my feature and to add sample data. The `VehiclesModel` is designed to represent data with the necessary properties. In an effort to enhance my approach, I introduced `VehiclesServices` to decouple the logic from the presentation layer. `VehiclesServices` is responsible for key operations such as retrieving the list of vehicles, filtering the list by a search string, and fetching vehicle details by their ID. The presentation layer, encapsulated within `VehiclesPage`, interfaces with the database to load data and manage UI events. For instance, it performs free-text filtering by invoking the filtering method from `VehiclesServices` and displays details for a selected vehicle.

Here are some examples that were newly implemented and highlight good software practices.

This week, I want to focus on my `VehiclesServices` class, which contains several software engineering principles. Separating the business logic layer from the presentation layer is itself a good software engineering practice because it allows us not to rely on the different layers. This means that changes in one part of the system, such as database schema changes, have minimal impact on other parts like the business logic or the UI.

```
    /// <summary>
    /// Services for managing vehicle data
    /// </summary>
    public class VehiclesServices
    {
        /// <summary>
        /// The database interface for accessing vehicle data
        /// </summary>
        private readonly IVehiclesDB _vehicleDatabase;

        /// <summary>
        /// Constructor
        /// </summary>
        /// <param name="vehicleDatabase">The vehicle databas</param>
        public VehiclesServices(IVehiclesDB vehicleDatabase)
        {
            _vehicleDatabase = vehicleDatabase;
        }

        /// <summary>
        /// Retrieves all vehicles from the database
        /// </summary>
        /// <returns>List of vehicles</returns>
        public async Task<List<VehiclesModel>> GetAllVehicles()
        {
            return await _vehicleDatabase.GetAllVehiclesFromDB();
        }

        /// <summary>
        /// Filters vehicles based on free-text search
        /// </summary>
        /// <param name="filter">The filter</param>
        /// <returns>List of filtered vehicles</returns>
        public async Task<List<VehiclesModel>> FilterVehicles(string filter)
        {
            var allVehicles = await GetAllVehicles();
            return allVehicles.Where(v =>
                    v.VehicleId.ToString().Contains(filter) ||
                    v.Type.Contains(filter) ||
                    v.Status.Contains(filter) 
                ).ToList();
        }

        /// <summary>
        /// Retrieves details of a single vehicle by its ID
        /// </summary>
        /// <param name="vehicleId">The ID of the vehicle</param>
        /// <returns>The vehicle details</returns>
        public async Task<VehiclesModel> GetVehicleDetails(int vehicleId)
        {
            return await _vehicleDatabase.GetVehicleById(vehicleId);
        }
    }
```

Key **SOLID** principles introduced in this snippet of code are:

**Single Responsibility Principle**: Each method in the `VehiclesServices` class has a single responsibility: `GetAllVehicles` retrieves all vehicles, `FilterVehicles` applies a free-text filter to the vehicle list, and `GetVehicleDetails` retrieves details for a single vehicle. This makes the code easier to understand, test, and maintain.

**Interface Segregation Principle**: The use of the `IVehiclesDB` interface allows for greater flexibility. The actual data in `VehicleDB` can be swapped out without needing to change the business logic in `VehiclesServices`. This is useful for testing and also if the application needs to support different data in the future.

**Dependency Inversion Principle**: The `VehiclesServices` class acts as a client and receives data access from the service, which is `VehicleDB`. It is specifically designed to accept an instance of `IVehiclesDB` through its constructor. This practice, known as **Dependency Injection**, is a core aspect of the **Dependency Inversion Principle**, one of the **SOLID** principles. It ensures that the `VehiclesServices` class remains minimally dependent on the implementation details of data access. By relying on abstractions rather than concrete implementations, the class becomes more resilient to changes, easier to test, and simpler to maintain.

The provided snippet of code also introduces other good software practices like **meaningful names** - each method and variable has a meaningful name, making the code self-explanatory, which allows for the elimination of **unnecessary comments**.

## Testing

Implementing the `IVehiclesDB` interface allows the use of mocking frameworks, such as Moq, for unit testing. This approach simulates the behavior of the database and tests the operations of the `VehiclesServices` class in isolation, without the need for a live database connection. Such an improvement enhances the reliability and efficiency of the testing process, allowing a focus on the logic and behavior of the methods.

The test `GetAllVehicles_ReturnsAllVehicles` sets up a mock database using the `IVehiclesDB` interface, inserts sample data, and calls `GetAllVehicles` to check if the return count of the list is correct. This ensures that the method correctly returns all vehicles, without any dependency on a live database, for better isolation.

```
    /// <summary>
    /// Tests if GetAllVehicles method returns all vehicles
    /// </summary>
    [Fact]
    public async Task GetAllVehicles_ReturnsAllVehicles()
    {
        // Arrange
        var mockDb = new Mock<IVehiclesDB>();
        var vehicles = new List<VehiclesModel>
        {
            new VehiclesModel { VehicleId = 1, Type = "Truck", Status = "Active" },
            new VehiclesModel { VehicleId = 2, Type = "Car", Status = "Not Active" }
        };
        mockDb.Setup(db => db.GetAllVehiclesFromDB()).ReturnsAsync(vehicles);
        var service = new VehiclesServices(mockDb.Object);

        // Act
        var result = await service.GetAllVehicles();

        // Assert
        Assert.Equal(2, result.Count);
        mockDb.Verify(db => db.GetAllVehiclesFromDB(), Times.Once);
    }
```

The test `FilterVehicles_ReturnsFilteredVehicles`, similar to the previous test, sets up a mock database using the `IVehiclesDB` interface and inserts sample data. It then sets a filter and calls the `FilterVehicles` method with the filter as a parameter, and asserts whether the returned list is appropriately filtered. This ensures that the method correctly filters data.

```
    /// <summary>
    /// Tests if FilterVehicles method returns correct vehicles based on a given filter
    /// </summary>
    [Fact]
    public async Task FilterVehicles_ReturnsFilteredVehicles()
    {
        // Arrange
        var mockDb = new Mock<IVehiclesDB>();
        var vehicles = new List<VehiclesModel>
        {
            new VehiclesModel { VehicleId = 1, Type = "Truck", Status = "Active" },
            new VehiclesModel { VehicleId = 2, Type = "Car", Status = "Inactive" },
            new VehiclesModel { VehicleId = 3, Type = "Motorcycle", Status = "Active" }
        };
        mockDb.Setup(db => db.GetAllVehiclesFromDB()).ReturnsAsync(vehicles);
        var service = new VehiclesServices(mockDb.Object);
        var filter = "Truck";

        // Act
        var result = await service.FilterVehicles(filter);

        // Assert
        var filteredVehicles = vehicles.Where(v =>
            v.VehicleId.ToString().Contains(filter) ||
            v.Type.Contains(filter) ||
            v.Status.Contains(filter)).ToList();

        Assert.Equal(filteredVehicles.Count, result.Count);
        Assert.All(result, vm => Assert.Contains(filter, vm.Type));
    }
```

# Reflection

### Improvments 

This week, my work has significantly improved compared to last week, mostly by adapting solutions to the mistakes I made in the previous week.

When I create a branch for a new feature, I immediately check if I'm on the correct branch and if there are no other upstreams to the remote branches. I start using git commands in the terminal to double-check everything and rely less on the IDE. First, I create a new branch with the git command `git checkout -b name-of-branch`, then I check if my new branch doesn't have any remote upstreams by using `git branch -vv`, and then I check if there are no uncommitted changes on my local machine by using `git status`. Even though the git interface in Visual Studio is good and provides those functionalities, I think it's good to be familiar with the git commands, and I plan to operate using them from now on.

Separating the business logic layer from the data and presentation layers significantly improved my application of the principles of clean code. Creating a new class, VehiclesServices, allows me to isolate these three layers, resulting in a more straightforward approach. Now, if I want to enhance my feature, I don't need to make changes in different layers because they don't rely on themselves. This approach also makes testing easier; for example, implementing a filtering method in `VehiclesServices` class instead of `VehiclesPage` allows me to test this method.

Implementing a new class also allowed me to overcome my problem with mock testing. Dependency Injection, by passing the `IVehicles` interface of the VehiclesDB class, enables me to test the `VehiclesServices` methods without using an actual database. Last week, I thought that the problem was with my tests, and I'm happy that I didn't give up on mock testing because I learned about dependency injection, interfaces, and how they work.


