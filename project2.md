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
