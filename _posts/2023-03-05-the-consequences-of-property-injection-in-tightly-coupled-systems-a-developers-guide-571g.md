Developers may try to use property injection in a tightly coupled system for several reasons:

1.Ease of use: Property injection can be a simple and straightforward way to pass dependencies between objects, making it a popular choice for developers who are new to dependency injection.

2.Familiarity: Developers who are used to working with tightly coupled systems may find it more natural to use property injection, as it resembles the way dependencies are typically handled in tightly coupled systems.

3.Lack of planning: Developers may not have taken the time to plan out the architecture of the application and may make decisions on the fly that lead to the use of property injection in a tightly coupled system.

```
public class OrderService 
{
    private IOrderRepository _orderRepository;
    public IOrderRepository OrderRepository 
    {
        get { return _orderRepository; }
        set { _orderRepository = value; }
    }

    public void SaveOrder(Order order) 
    {
        // Use the injected repository to save the order
        _orderRepository.Save(order);
    }
}
```

```
class Program
{
    static void Main(string[] args)
    {
        // Create an instance of the repository
        var orderRepository = new OrderRepository();

        // Create an instance of the service and property inject the repository
        var orderService = new OrderService();
        orderService.OrderRepository = orderRepository;

        // Create a new order
        var newOrder = new Order { Id = 1, Customer = "John Doe" };

        // Save the order using the service
        orderService.SaveOrder(newOrder);
    }
}
```
In this example, the `OrderService `class has a property called `OrderRepository `of type `IOrderRepository`. The property setter is used to inject an instance of IOrderRepository into the `OrderService `class. The `SaveOrder `method then uses the injected repository to save the order. This type of injection is simple and easy, but it has some drawbacks.

It’s important to notice that the `OrderService `class is tightly coupled to the `IOrderRepository `interface and any class that implements it, making it difficult to change the implementation of the repository without modifying the `OrderService `class. Additionally, it's also not clear from the constructor of the `OrderService `class that the `IOrderRepository `is a required dependency, and it could lead to the class being used without the dependency being set.

It’s important to note that property injection could cause problems in the long run because it makes the dependencies between objects implicit and hard to reason about, and also does not provide compile-time safety. It’s better to avoid this kind of design and invest time to plan the architecture of the application in a way that separates the concerns and make the application more maintainable and scalable.