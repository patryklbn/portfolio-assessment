# Code review 

This portfolio entry highlights my skills in C# code review and refactoring. It demonstrates how I detect coding issues and improve them using best practices.

The featured code was selected from a series of C# code snippets I reviewed. Each snippet presented in the challenge had one or more issues, and the goal was to spot and rectify them. 

### Original code
```
class Ticket
{
    public int TicketNo;
    public string Customer;
    public DateTime CallDate;
    public string Description;
    public int Priority;
    
    public static void Main(string[] args)
    {
        var ticket = new Ticket
        {
            TicketNo = 1,
            Customer = "John Doe",
            CallDate = new DateOnly(2023, 4, 1),
            Description = "Lost password",
            Priority = 1
        };
    }
}
```

### Problem identified
**Primitive Obsession**: A major issue with the the original code is the representation of a `Customer` as a string value. Such design leans excessively on primitive types when a more structured object would be appropriate. Relying on primitive obsession constrains potential code evolutions and restricts opportunities for enhancing the design in the future.

### Improved version of the code
```
public class Customer
{
    public string Name { get; private set; }

    public Customer(string name)
    {
        Name = name;
    }
}

public class Ticket
{
    public int TicketNo { get; private set; }
    public Customer Customer { get; private set; } 
    public DateTime CallDate { get; private set; }
    public string Description { get; private set; }
    public int Priority { get; private set; }

    public Ticket(int ticketNo, Customer customer, DateTime callDate, string description, int priority)
    {
        TicketNo = ticketNo;
        Customer = customer;
        CallDate = callDate;
        Description = description;
        Priority = priority;
    }
}

class Program
{
    public static void Main(string[] args)
    {
        var customer = new Customer("John Doe");
        var ticket = new Ticket(1, customer, new DateTime(2023, 4, 1), "Lost password", 1);
    }
}
```
### Explanation
My refactoring approach primarily focused on the issue of **Primitive Obsession** by converting from a string type to a structured `Customer` class. This change made the code clearer and also provided room for potential extensions. Now, additional attributes like address or phone number can be added to the `Customer` class without affecting the `Ticket` class. 

### Additional refactoring 
While completing the challenge, I might have overthought the task, since **Primitive Obsession** turned out as the only issue with the code. However, I did some additional refactoring which I believe is good practice. In the `Ticket` class, I implemented encapsulation by hiding data in private fields and exposing them through public properties, enhancing the security of user data. I also changed the object from `DateOnly` to `DateTime` to match the field type. When creating the objects, attributes are set directly via constructor parameters. A `Customer` object is created with the name _"John Doe"_, and then a `Ticket` object is established with several attributes, including the newly created Customer object. Additionally, I moved the **Main method** to the class level to separate it from the business layer.





