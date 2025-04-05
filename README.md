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
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

```
The Observer Pattern allows for the implemenation of different subscribers types with the
same behavior. However, using a Rust trait allows for these subscribers to have their own different behaviors. Moreover, a trait lets these subscribers use shared or necessary behaviors. For example, the update method for attributes.

In this case, a single Model struct is enough because there is only one type of
subcriber implemented. However, if there were different types of
subscribers to be implemented in the future such as email subscribers then a Rust trait
should be used.

```

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

```
A notification system is bound to have many subscribers, so the DashMap is necessary for quick searches. The DashMap's three main functions all have time complexity O(1). Searching will always be fast even with big lists due to constant-time access. Moreover, unique IDs and URLs can be mapped easily as it follows the base structure of Maps' key-value structure. It's also safe for multithreaded applications. Thus, DashMap is most suitable for the BambangShop case.

On the other hand, using Vec can be sufficient only if the Subscriber list is small. However, `Vec` is much slower than the Dashmap as it has to iterate through lists with time complexity O(n). The performance speed drops significantly as the list grows.
```

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

```
DashMap has thread safety and allows multiple threads to access or modify the map without manual locking.
Without DashMap, to ensure thread safety Mutex has to be used which may make the code more complicated to modify or extend to it.
While Singleton ensures that there is only a single instance of the Subscriber map.
Without Singleton, there could be redundant duplicate maps.
Since this application is multithreaded, both DashMap and Singleton are necessary.
```

#### Reflection Publisher-2

1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
```
In this case, the business logic and data storage are handled by
"Service" and "Repository" respectively. If these two modules were
combined into one "Model" like in the MVC pattern then the Single Responsibility Principle and Seperation of Concerns Principle are violated. This reduces the code's extensibility, scalability and maintainability and makes it messier and harder to modify. The code
is also harder to test. Seperating the modules makes the code more
adaptable to changes such as change in business logic or databses.
 
```

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?
```
As mentioned before, if the Model is only used then it will have two
functions. The unflexible code and its struct models will be tightly coupled with each other, making it harder to maintain, scale, extend and test. For example, the Notification and Subscriber structs will share so many dependencies that altering the business logic or changing the database might cause bugs or problems in the other. More functions might
even have to be made to accomodate changes which could be avoided if
only one Model is not used. 
```

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
```
With Postman, I can test web endpoints and assess the behavior of the application based on its outputs. I like how API requests can be saved or compiled into collections which allows for easy access and fast testing. One of the helpful features is the mock servers which lets us test frontends without a backend. Another feature is automated testing, which allows faster testing and is helpful in Group Projects where time is limited. The API documentation is especially useful as it can be used by all members in a team. Postman helps me test if the backend is fully prepared and working with realistic user input behaviors.
```

#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
```
In this tutorial, the Observer Pattern's Push model is used. All the subscribers immediately receive notifications which are pushed by the Publisher or Notification service whenever they make a change. These changes include create, delete or update of a product. The subscribers do not have to request notifications from the Publisher because it pushes notifications based on subscriber actions.
```

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)
```
If a Pull model is used, the Publisher does not decide when to send notifications but the subscribers do. 
The advantages include more control for the subscribers, they can decide when and what type of notifications to receive. That way subscribers can request notifications later if they are busy. The Publisher could save resources since it is not active the whole time unlike in the Push model, resulting in faster performance.
A disadvantage is subscribers have to manually check for update notifications, they could miss out on important or time-sensitive updates such as limited discounts if busy. Moreover, there will be delays if multiple subscribers request simultaneously and repeatedly where a request cooldown feature has to be implemented. 

```

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.
```
The application will become much slower as it stops other processing notifications while it waits for one to finish. Upcoming notifications are queued before being processed and sent to their respective subscribers. The application will be very unresponsive because of long delays and inefficient at handling large number of subscribers.
```