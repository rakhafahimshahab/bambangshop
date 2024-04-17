# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
##### Interface vs. Model Structure in Rust
- Interface in Observer Pattern: Typically, the Observer pattern involves an interface that observers (subscribers) must implement. This design allows different types of observers to register with the publisher and receive updates without the publisher needing to know the specific types of observers.
- Trait in Rust: In Rust, traits serve a similar purpose as interfaces in other languages. A trait defines a set of methods and behaviors that a type must implement, making it suitable for the Observer pattern.
- Decision: Reflect on whether BambangShop requires multiple types of subscribers that might behave differently or if a single model struct is sufficient. If all subscribers will use the same set of behaviors, a model struct may be adequate. However, if future extensions might introduce different types of subscribers, using a trait would offer more flexibility.

##### Data Structure for Subscriber Management 
- Using Vec vs. DashMap: Consider the operations you need to perform on the list of subscribers. Vec (a list) would typically allow you to iterate over subscribers and send notifications, but might not be efficient for operations like adding or removing subscribers if they need to be identified uniquely and quickly.
- DashMap: This is a concurrent, thread-safe HashMap. It allows quick lookups, additions, and deletions using unique keys, making it suitable for managing a dynamic list of uniquely identified subscribers.
- Evaluation: Assess the importance of performance for operations such as adding or removing subscribers, and the potential size of the subscriber list. If high concurrency and quick modifications are required, DashMap or a similar thread-safe data structure might be necessary.

##### Use of DashMap vs. Singleton Pattern 
- DashMap for Concurrency: DashMap is used here for its thread-safety features, which are crucial when multiple threads might modify the list of subscribers concurrently.
- Singleton Pattern: This pattern involves creating a single instance of a class that is accessible globally. While it provides a global point of access to the resource (like SUBSCRIBERS), the singleton itself does not ensure thread safety.
- Decision: Consider whether the Singleton pattern would suffice for managing global access while maintaining thread safety, possibly using Rust's concurrency features like mutexes. However, if the simplicity and built-in thread safety of DashMap effectively meet the project's needs without additional complexity, it might be the better choice.

#### Reflection Publisher-2
##### Separation of "Service" and "Repository" from a Model
- MVC Model Responsibilities: In the traditional MVC pattern, the model manages the data, logic, and rules of the application. However, combining data access logic and business logic in a single model can make the code less maintainable and more complex.
- Why Separate?: Separating "Service" (business logic) and "Repository" (data access) from the Model ensures each component has a single responsibility. This separation enhances maintainability, scalability, and testability of the application. Services coordinate tasks and apply business rules, while Repositories handle data storage and retrieval without business logic.
- Benefits: This approach adheres to the Single Responsibility Principle and can lead to cleaner, more organized code that is easier to manage and test.

##### Implications of Using Only the Model
- Increased Complexity: If business logic and data access are not separated from the model, each model becomes more complex and harder to maintain. It would need to handle not just data properties but also complex interactions and transformations.
- Code Coupling and Maintenance Issues: Mixing data access with business logic can lead to tightly coupled code that is difficult to modify and extend. For instance, changing the data source or business rule might require changes to the model, affecting other parts of the application that use the same model.
- Example: Consider a scenario where changes to the notification logic (like conditions under which notifications are sent) require modifications to the database schema or queries. If these aspects are entangled within a single model, each change could necessitate significant testing and adjustment across related functionalities.

##### Exploration of Postman
- Tool for API Testing: Postman is a powerful tool for testing APIs. It allows you to send requests to the server and inspect the responses without writing any client code. This can speed up testing and debugging during development.
- Features of Interest: Postman can automate tests for APIs, ensuring that each endpoint behaves as expected after changes. This is particularly useful for regression testing. You can set up different environments (e.g., development, production) in Postman, which makes it easier to test different configurations or stages of your application without changing your code. Allows teams to share collections of API requests, ensuring that everyone is testing with the same parameters and settings.
- Benefits for Projects: Discuss how these features can help in your group project and future software engineering tasks, particularly in ensuring that your back-end services work correctly before integrating them with front-end applications.

#### Reflection Publisher-3
##### Observer Pattern Variations Used in the Tutorial
- Push Model: In the push model, the publisher actively sends notifications to the subscribers without the subscribers requesting the information. Given the tutorial's description of the system where the main application sends notifications via HTTP POST requests whenever a product is created, deleted, or promoted, it aligns with the push model of the Observer pattern.
- Key Observation: The tutorial implements functions in the NotificationService that prepare and send data directly to subscribers, which is a hallmark of the push model.
##### Imagining the Use of the Pull Model
- Pull Model: In this model, subscribers periodically request information from the publisher rather than receiving it automatically.
- Advantages of Pull Model: Subscribers only pull data when necessary, which could reduce unnecessary network traffic and data transmission.Subscribers can ensure they receive the most current data exactly when they need it.
- Disadvantages of Pull Model: Subscribers must implement timing and logic for data requests, increasing complexity on the client side.If subscribers do not request updates frequently enough, they might miss out on timely information.
- Impact on BambangShop: For an e-commerce setting where timely updates about product statuses are crucial, relying on subscribers to pull information might delay the reception of important updates like new product availability or promotions.
##### Impact of Omitting Multi-threading in Notifications
- Without Multi-threading:
Sequential Processing: Notifications would be processed one at a time, leading to potential delays in notifying all subscribers.
Performance Bottleneck: In scenarios with a large number of subscribers, sequential processing could significantly slow down the notification system, affecting the user experience by delaying updates.
Potential Overload: During high-traffic periods, such as during a product launch or sale, the lack of multi-threading could lead to an overload of the system, as it struggles to process a surge in notifications.
- Overall Impact: Not using multi-threading could undermine the efficiency and responsiveness of the notification system, which is critical in maintaining a real-time feel for subscribers in e-commerce environments.