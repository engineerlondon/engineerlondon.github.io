---
layout: post
title:  "Technical Topics"
date:   2020-01-30 09:00:00 +0000
categories: general
mathjax: true
---

The following is a list of technical topics that I have been asked about in interviews, it is a high level revision sheet, designed to prompt rather than be a full explaination. I highly reccomend taking a copy and augmenting it to match your needs.

If you find anything that does not read well or you do not think is accurate, as always please raise an issue here: [GitHub Issues engineerlondon.co.uk](https://github.com/engineerlondon/engineerlondon.github.io/issues). Any input will be gratefully recieved.

# Relational DBs
## Concurrency control
  - If transactions are executed serially, i.e., sequentially with no overlap in time, no transaction concurrency exists. However, if concurrent transactions with interleaving operations are allowed in an uncontrolled manner, some unexpected, undesirable results may occur, such as:
    - The lost update problem: Two simultaneous transactions are executed, both read data from the database, one updates the record, then the other updates the record, the first update is lost.
    - The dirty read problem: A transaction is in progress, hits an error and beings to revert the changes, before the change is reverted a second translation occurs using the “Dirty Data”.
    - The incorrect summary problem: If one transaction is calculating an aggregate summary function on a number of records while other transactions are updating some of these records, the aggregate function may calculate some values before they are updated and others after they are updated.
  - ACID – most relational database implementations are ACID compliant:
    - Atomicity (“All or nothing”):
      - This property ensures that either the transaction occurs completely or it does not occur at all.
      - The database management system maintains the atomic nature of transactions in spite of any DBMS, operating system, or hardware failure.
    - Consistency:
      - Only valid data will be written to the database (constraints are enforced). If part of a transaction would result in data inconsistency the transaction will be rolled back.
    - Isolation:
      - Transactions can occur simultaneously without causing any inconsistency.
      - The resultant state of the system after executing all the transactions is same as the state that would be achieved if the transactions were executed serially.
      - Prevents intermediate data from being read by a different transaction.
    - Durability:
      - All changes made by a transaction after its successful execution are persisted (usually to disk).
      - Changes are not lost even if there is a failure.

## Clustered / non-clustered / mirrored database infrastructure
- Clustering:
  - Improves resilience to failure (ie a database node going offline).
  - Comparatively cheap scaling (especially for high volume read operations).
  - For high volume data modifications (insert, update or delete) contention can be a problem, as the data is synchronised across the nodes. An alternative that is common when using Microsoft Sql Server is Database Mirroring, which has the option of using “High-performance mode”, this comes at a cost of having a "master slave" configuration, some data loss can occur if a forced role switch is required for example if the master suffers a disaster.
- Non-clustered (eg master slave or single node):
  - Simpler to manage.
  - Traditionally quicker to get started.
  - Fewer nodes.
  - Not necessarily cheaper depending on how much load and type of load you want to support.
  - Generally speaking a single instance or master slave configuration can out perform a small cluster for Create, Update, Delete operations.
  - Increased risk of an outage or data loss.

## Clustered and non-clustered indexes (improve lookup performance):
- Clustered index:
  - **Created by ordering the data in the table.**
  - Max one per table.
  - Primary key will be the clustered index (if set).
  - Do not require additional space
- Non-clustered:
  - Can decrease Insert performance.
  - Require additional space both disk and memory to store and use the index.

- Is there any point to adding an index to a datetime field?

# NoSQL (or "Not only SQL")
- Simpler "horizontal" scaling
- Reduces “object-relational impedance mismatch” (ie: frequently relational data does not map well to OO objects for a number of reasons, a NoSQL document store serialises objects to  documents, frequently storing the data as json or bson).
- Most NoSQL stores are not ACID compliant, usually because they compromise consistency in favour of performance, which is often fine if you are working with “Big Data”. They do however implement **"eventual consistency"** (ie the data will be recorded correctly once it has synchronised to all nodes).
- Some NoSQL systems may exhibit lost writes.

# C# #

## Generics
- Generics make it possible to design classes and methods that defer the specification of one or more types until the class or method is declared, generics are replaced with a specific type at compile time.
- Code will still be type safe, but avoids writing the same code for every type of object, a practical example is the Stackoverflow C# auto retry method (Q: 1563191), pass in any method, with any return type, it will be auto retried and return the method's normal return type. (it also makes use of delegates in the form of Func<T> where T is the generic return type.) [https://stackoverflow.com/questions/1563191](https://stackoverflow.com/questions/1563191)

`public static T Do<T>(Func<T> function, TimeSpan interval, int maxRetry = 3)
{
  // implementation of retry,
  // returns action();
}`

Usage:

`int result = Retry.Do(SomeFunctionWhichReturnsInt, TimeSpan.FromSeconds(1), 4);`

- This method has a Generic return type T and it must be passed a type of Function.
Makes use of built-in generic delegate [Tutorials Teacher Generics](https://www.tutorialsteacher.com/csharp/constraints-in-generic-csharp).

- If you are using retry code in practice read this: [docs.microsoft architecture patterns retry](https://docs.microsoft.com/en-us/azure/architecture/patterns/retry).
- And this The Poly lib: [Microsoft retries exponential backoff](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)

## Anonymous methods:
 - Can be passed as a parameters, commonly used as event handlers:

`saveButton.Click += delegate(Object o, EventArgs e)
						{
							logger.Info(“button clicked”);
						};`

# MVC
[https://www.tutorialsteacher.com/mvc/action-method-in-mvc](https://www.tutorialsteacher.com/mvc/action-method-in-mvc)
## Model binding:
- DefaultModelBinder -
- RouteData is a property of the base Controller class, RouteData can be accessed in any controller. RouteData contains route information of a current request. You can get the controller, action or parameter information using RouteData.
- ModelBinding is the mechanism ASP.NET MVC uses to create strongly-typed objects (or fill primitive-type parameters) from the input stream (usually an HTTP request).
- Why not use manual de-serialisation (cutting out the middle man) VS Model binding:
	- Model binding enables simple good user experience eg:
		- One field matches the model but one does not, deserialisation of the object would fail, whereas we can get the specific information for an individual field.
		- Limiting the range for an input field can be achieved easily using an attribute and the code to handle multiple simple attributes is already built in, for more complex limitations a Validation Attribute can be used (by implementing the ValidationAttribute interface).
[Microsoft Core MVC model binding](https://haacked.com/archive/2011/06/30/whatrsquos-the-difference-between-a-value-provider-and-model-binder.aspx)

Custom Input Formatter - allow complex input data to be read into a model.
- Custom Validation - Allows validation to be added to a model in an extensible way [SO 16100300 custom validation](https://stackoverflow.com/questions/16100300/asp-net-mvc-custom-validation-by-dataannotation)

Or my own variant on the same theme:
{% highlight Csharp %}
public class CombinedMaxAttribute : ValidationAttribute
{
    /// <summary>
    /// Checks the combined size of two properties, based on Stack Overflow Q 16100300,
    /// </summary>
    /// <param name="maxDifference">The largest allowed difference in size</param>
    /// <param name="propertyName">The name of the other property that combined should not have a total difference than max difference.</param>
    public CombinedMaxAttribute(int maxDifference, string propertyName)
    {
        PropertyName = propertyName;
        MaxDifference = maxDifference;
    }

    public string PropertyName { get; }
    public int MaxDifference { get; }

    protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    {
        var properties = validationContext.ObjectType.GetProperty(PropertyName);
        if (properties != null)
        {
            var values = properties.GetValue(validationContext.ObjectInstance, null);
            var totalLength = Math.Abs(Convert.ToInt32(values) - Convert.ToInt32(value));

            if (totalLength > MaxDifference)
            {
                return new ValidationResult(this.FormatErrorMessage(validationContext.DisplayName));
            }
        }

        return null;
    }
}
{% endhighlight %}

## C# MVC Actions:
- All the public methods of a Controller class are called Action methods. Like any methods with the following restrictions:
  - Must be public. (They cannot be private or protected.)
  - Cannot be overloaded.
  - Cannot be static.
- Action selector is an attribute that can be applied to an action method to help the routing engine to select the correct action method for a particular request.
- C# MVC 5 includes selector attributes:
     - ActionName
     - NonAction
     - ActionVerbs
     - HTTP Verb - used to annotate a method with the HTTP method that the method supports.

# HTTP Verbs:
 - GET – Retrieve data (optionally pass in parameters eg: GetById(studentId)).
 - POST – Submit data (usually using form / body).
 - PUT – Used to create a resource, or overwrite it. While you specify the resources new URL. [Stack Overflow PUT vs POST](https://stackoverflow.com/questions/630453/put-vs-post-in-rest)
 - PATCH – Full or partial update to a resource / record.
 - DELETE – Delete a record.
 - HEAD – Same as GET but does not return a result/body, just the headers.
 - OPTIONS – Request details about how to communicate.
[Martin Fowler](https://martinfowler.com/articles/richardsonMaturityModel.html)

# Object Oriented Programming (OOP) Design patterns
- [https://csharpindepth.com/articles/singleton](https://csharpindepth.com/articles/singleton)

Singleton:
- There are issues with the Singleton pattern when it comes to unit testing, however it is commonly used.
- Avoid creating multiple instances of an object.
- Simplifies access by automatically creating an instance if it has not already been created and only ever allowing one instance to be created.

{% highlight Csharp %}
public sealed class Singleton
{
  private static readonly Singleton instance = new Singleton();

  // Explicit static constructor to tell C# compiler
  // not to mark type as beforefieldinit
  static Singleton()
  {
  }

  private Singleton()
  {
  }

  public static Singleton Instance
  {
    get { return instance;}
  }
}
{% endhighlight %}

# Object Oriented Programming (OOP) Design principles

- **Encapsulation** - Group related properties and methods together (in a class).
- **Abstraction** - Each object only exposes the high-level mechanism's required, ie hide internal implementation details.
- **Inheritance** - Reuse common logic and extract unique logic into a separate class.
- **Polymorphism** (many shapes) - Derived classes can be treated as their parent class,
- [freecodecamp.org Object Oriented Programming](https://www.freecodecamp.org/news/object-oriented-programming-concepts-21bb035f7260)

## Abstract VS Interface
- An interface defines the methods and properties that must be implemented to satisfy the interface. All methods and properties must be public since it has no implementation.
- An Abstract class cannot be instantiated, instead it must be sub classed to allow the implementation of any abstract methods it contains. Methods in an abstract class can be private.

## SOLID principles
- **S** - Single-responsibility principle: A class should have one and only one reason to change, meaning that a class should have only one job.
- **O** - Open-closed principle:
  - Objects or entities should be open for extension, but closed for modification.
  - A class should be easily extendable without modifying the class itself.
  - In the Coffee machine example the writer states that once we add a second coffee machine we should abstract the coffee machine using an interface ICoffeeMaker so that when we replace the basic CoffeeMaker we do not need to change the CoffeeMaker App, instead we just pass in the PremiumCoffeeMaker to the App. (this is supported by dependency injection).
- **L** - Liskov substitution principle (https://stackoverflow.com/questions/56860)
  - Every derived class should be substitutable for their base/parent class.
  - Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.
  - You should model classes based on behaviours not on properties.
  - A sub type must maintain the behaviour specified for the base type.
  - Eg: derived class square with base class rectangle breaks the Liskov principal, because you end up setting both sides of a square but only one side of a rectangle.
  - Don’t implement any stricter validation rules on input parameters than implemented by the parent class.
  - Apply at the least the same rules to all output parameters as applied by the parent class.
- **I** - Interface segregation principle:
  - ISP is focused on the idea of each interface representing one discrete and cohesive behaviour.
  - That is, each logical group of things an object should do would map to a single specific interface. A class might want to do several things, but each thing would map to a specific interface representing that behaviour. The idea is each interface is very focused.
  - For the Basic and PremiumCoffeeMachine example we could have:
  - `IPremiumCoffeeMaker : ICoffeMaker, with method MakeExpresso();`
  - `IBasicCoffeeMaker : ICoffeeMaker, with method MakeFilter();`
- **D** - Dependency Inversion Principle:
  - High level modules should not depend upon low-level modules. Both should depend upon abstractions.
  - Abstractions should never depend upon details. Details should depend upon abstractions. - Changes are risky, and by depending on a concept instead of on an implementation, you reduce the need for change at call sites.
  - Enables the reuse of software components which rely upon external dependencies.
  - [Microsoft - Architecture Modern Web Apps Dependency Inversion](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/architectural-principles#dependency-inversion)

# Dependency Injection
Enables a single place for registering an implementation of code that will be used throughout the application, eg: logger, database connection, external libraries.
- Supports SOLID engineering principles
Cons:
- Learning curve
- Can break parts of an IDE eg: find usages.
- Can shift errors to runtime, that would otherwise be compile time errors.

Example of registering a dependency in .Net Core:

`public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    // register our implementation
    services.AddTransient<IExampleInterface, DemoConcrete.Implementation>();
}`
And in your MVC Controller:

`public HomeController(ILogger<HomeController> logger, IExampleInterface dataSource)`

# Security
## Encryption and hashing
- Encryption is a mechanism used for protecting data.
#### Asymmetric Encryption:
- Provides confidentiality, authenticity and non-repudiation of electronic data.
- Use a public key (to encrypt the data) and a private key to decrypt the data, if someone has your private key they can decrypt the data. By intercepting the communication.
- At the start of an encrypted communication there is a key handshake, where the public key of both parties are sent to each other. And the type of encryption to be used is communicated.
- Then all subsequent communication can be wrapped using an encryption method (like a C# encryption library which provides SHA256 / SHA512).
- Both parties then send messages back and forth using the other systems public key, and decrypting using the private key.
- There are a number of additional security features built into SSL and other protocols:
 - We want to check that the public key being received is trusted, the Public key has a section devoted to the mapping of the Certificate's Trust Chain, ie the chain of certificates that leads to a known trusted certificate, eg: Verisign's public key.
 - In C# by default this trust chain check is enabled.
 - To increase security you can have a different key for each session, say change the key every 15 mins.
 - Asymmetric encryption requires more computation than Symmetric encryption.
 - Commonly used for protecting communications eg: SSL on a login / payment.
 - SSL is now a standard rather than the exception for most websites, largely driven by a number of Google initiatives. [Google Blog 2014 https as ranking signal](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html).
 - [docs.microsoft.com dotnet wcf working with certificates](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates)

# Symmetric encryption
- Only one key is used to encrypt the data, the same key is used to decrypt.
- Commonly used for protecting data at rest, eg: Bitlocker.

# Two factor authentication
- The use of 2FA increases the security of an individual's account, particularly from their password being exposed, ie even if the individual's username and password have been compromised, 2FA will both alert the individual to an attempted breach of security and stop the breach from progressing. Typically this can be done by sending a message with a one time password to the person, eg texting a pin number for each login.

# Asynchronous (async / await)
{% highlight Csharp %}
Task<Toast> toastTask = ToastBread(2);
Task<Egg> eggTask = FryEggs(2);
Egg eggs = await eggTask;
Toast toast = await toastTask;
ApplyButter(toast);
{% endhighlight %}

- You should move the Apply Butter method inside a new MakeToastWithButter method which returns a Task<Toast\>

- You can use `await Task.WhenAll(toastTask, eggTask)` or WhenAny
RESTful architectures

# REST APIs
## Hypermedia controls (HATEOAS)
- Introduces links to other actions that can be carried out on the element that was requested.
- This allows servers to change their URI scheme without breaking clients.
- Helps client developers explore the API
- [Wikipedia HATEOAS example](https://en.wikipedia.org/wiki/HATEOAS#Example)

{% highlight JSON %}
GET /accounts/12345/ HTTP/1.1
Host: bank.example.com
Accept: application/vnd.acme.account+json
...
{% endhighlight %}

{% highlight JSON %}
`HTTP/1.1 200 OK
Content-Type: application/vnd.acme.account+json
Content-Length: ...

{
    "account": {
        "account_number": 12345,
        "balance": {
            "currency": "usd",
            "value": 100.00
        },
        "links": {
            "deposit": "/accounts/12345/deposit",
            "withdraw": "/accounts/12345/withdraw",
            "transfer": "/accounts/12345/transfer",
            "close": "/accounts/12345/close"
        }
    }
}
{% endhighlight %}

## Versioning - [Semantic Versioning](https://semver.org)
- Swagger recommend semantic versioning ie major.minor.patch
- Major version when you make incompatible API changes
- Patch and minor version MUST be reset to 0 when major version is incremented.
- Minor version when you add functionality in a backwards compatible manner
- Patch version MUST be reset to 0 when minor version is incremented.
- PATCH version when you make backwards compatible bug fixes.
- URL versioning (Major only) and HTTP header versioning – is first and foremost a release management strategy.
- URL versioning has the advantage of being very visible, and testable through a browser.
- HTTP header versioning avoids the hypermedia issue where links can point to an old version, but are less visible.
- Consider consumer based testing if services are provided by a large organisation. Ie providers of a service can check if they have broken aspects of the interface that the consumer is expecting.
- [Martin Fowler enterprise REST](https://martinfowler.com/articles/enterpriseREST.html)
- RFC2616 - [Hypertext Transfer Protocol RFC](http://pretty-rfc.herokuapp.com/RFC2616)

# Types of caching:

- Geographical caching eg: language files will be used predominantly by specific countries.
- Time based cache, some data is only relevant for a period of time, eg: keeping a customer's data in a cache for 15 mins since their last request will allow the site to serve data more efficiently.

# Algorithms

## Big O Notation basics:
- Big O gives common language to describe how a function's performance or memory requirements scale.
- We talk about the "Average Case" and the "Worst case".
- We simplify a function's Big O definition in two ways:
- **Drop non dominant terms** when summing, eg: \\(O(N^2 + N)\\) `=>` \\(O(N^2)\\).
  - Its not that the code isn't doing that work, but that the work is insignificant when compared to the \\(N^2\\) work.
- We also **drop constants**
- In mathmatics the language is more accurate than the computer science (CS) equivalent, in Maths we use:
  - Omega Ω – Best case
  - Theta θ – average case
  - Big O – Worst case
- However in CS we simply talk about the concept of Big O, eg: Quick Sort has an average runtime of \\(O(n log n)\\), but the worse case is \\(O(n^2)\\).

## Sorting algorithms:
- Merge Sort - average and worst case runtime O(n log(n)), memory depends.
  - Divides the array in half, sorts both of those halves, then merges them back together, recursively, eventually you are merging two single element arrays.
  - The Merge method does the heavy lifting.
- Quick Sort - average runtime \\(O(nlogn) \\), worst case \\(O(n^2)\\), memory \\(O(log(n))\\)
  - Pick a random element, then partition the array so that all numbers that are less than the partitioning element are swapped to one side and all numbers greater than the partitioning element are swapped to the other side, repeat swapping until all elements are on the correct side of the partition.
- Bucket Sort:		Worst case runtime \\(O(n^2)\\)
- Bubble Sort: 		Worst case runtime \\(O(n^2)\\), Memory \\(O(1)\\)
- Selection Sort: 	Worst case runtime \\(O(n^2)\\), Memory \\(O(1)\\)
- Heap Sort: 		\\(O(n log(n))\\)
  - An improved Selection Sort, The improvement consists of the use of a heap data structure rather than a linear-time search to find the maximum.
- Radix Sort:		Worst case runtime \\(O(nk)\\)
  - Only works with whole numbers (integers), takes advantage of the fact that integers have a finite number of bits.
  - Where n is the number of elements and k is the number of passes of the sorting algorithm.
  - [stackoverflow difference between bucket and radix sort](https://stackoverflow.com/questions/4461737/what-is-the-difference-between-bucket-sort-and-radix-sort)

# Automated testing

# Unit testing:
- Read: [https://xp123.com/articles/3a-arrange-act-assert](https://xp123.com/articles/3a-arrange-act-assert)
- Arrange, Act, Assert, should be implemented in the inverse order ie:
  - Write your assert: `Assert.That(expected, Is.EqualTo(result));`
  - Write the action: `var result = GetStudent(searchCriteria);`
  - Lastly add the Arrange: `var searchCriteria = new SearchCriteria(){Id=10};`

# Test frameworks:
- NUnit: A stable testing framework for C# much the same as Xunit.
- Xunit: Unit testing framework like NUnit, but newer with certain features removed.
- Selenium: Browser automation tool, allows the real published UI to be tested, and enables performance test.

# Mocking / Proxy testing:
- Mocks: There are a lot of frameworks used for mocking eg: Mockito
- Proxy based mocking: A proxy is just an object which will be used instead of the original object. If a method of the proxy object is called than the proxy object can decide what it will do with this call:
  - Delegate it to the original object.
  - Handles the call itself.
  - `Mockito.mock(Foo.class)` - Creates a proxy object for the Foo class.
  - Limits of Proxy, It is not possible to:
      - Intercept static method calls.
      - Intercept private method calls.
      - Intercept final method calls.
      - Build a proxy for a final class.
- Classloader remapping based Mocking (Isolator.Net)
- This is a powerful tool for “Legacy Code”, code without tests, but only really necessary if you do not have access to the code, or are carrying significant risks by changing the code, it allows you to put the code under test but does nothing to address the reason you cannot write a normal test, it addresses the symptom not the cause.
- [Martin Fowler Modern Mocking Tools](https://martinfowler.com/articles/modernMockingTools.html)

# Refactoring:
- We tend to read code more than write it, anything we can do to reduce the need to read code is time well invested in the life of a project. - https://martinfowler.com
- Ensure we are implementing the SOLID principals.
- Use extract method and extract class to safely breakup large blocks of code.
- Compare the code with known patterns and consider implementing a standard pattern.
- Review variable naming for readability.
- Replace magic numbers with a variable (const), eg: `if(task.StatusId == 3)` can be changed to `if(task.StatusId == cancelledId)` where cancelledId is `const int cancelledId = 3`.

# Application architecture
- Layers VS tiers
  - A layer - a part of your code, if your application is a cake, this is a slice.
  - A tier - a physical machine, a server.

# Microservices:
Positives:
- Microservices can adapt easily to other frameworks or technologies.
- Failure of a single process does not affect the entire system.
- Provides support to big enterprises as well as small teams.
- Can be deployed independently and in relatively less time.
- Amazon's policy is that the team implementing a microservice should be small enough that they can be fed by two pizzas.

# SOA (service orientated architecture):
- Logically represents a business activity with a specified outcome.
- A service will embody the Interface separation principal, it is a set of related functionality that allows a customer to do what they need to, and no more. Ie you could have a service for Customer and it will allow the lookup and management of their health record covering: Age, Gender, Height, BMI, etc. But the service will not provide Purchase history, since this can be conceptually moved into a Purchase History service and has nothing to do with their health record.

# Concurrency
- Process:
  - Each process provides the resources needed to execute a program. A process has a virtual address space, executable code, open handles to system objects, a security context, a unique process identifier, environment variables, a priority class, minimum and maximum working set sizes, and at least one thread of execution. Each process is started with a single thread, often called the primary thread, but can create additional threads from any of its threads.
- Thread:
  - A thread is an entity within a process that can be scheduled for execution. All threads of a process share its virtual address space and system resources. In addition, each thread maintains exception handlers, a scheduling priority, thread local storage, a unique thread identifier, and a set of structures the system will use to save the thread context until it is scheduled. The thread context includes the thread's set of machine registers, the kernel stack, a thread environment block, and a user stack in the address space of the thread's process. Threads can also have their own security context, which can be used for impersonating clients.
  - Tasks/Thread - A thread is the bare metal thing, you probably don't need to use it, you can use a LongRunning task to benefit from the Task Parallel Library.
  - Generally unless you are I/O bound eg: database calls, read/write files, APIs calls, use LongRunning tasks. Because using tasks would lead you to a thread pool with a few threads busy and a lot of another tasks waiting for its turn to take the pool.
  - Or calling unmanaged code with unmanaged input data eg: Forensics data extraction.
- Tasks:
  - Abstraction above the Threads. Uses the thread pool (unless you specify the task as a LongRunning operation, if so, a new thread is created under the hood for you).
- Thread Pool
  - As the name suggests: a pool of threads. Is the .NET framework handling a limited number of threads for you to avoid the overhead of creating, scheduling and tearing down threads, which is comparatively expensive.

# Messaging

## Dead-letter queues
- A service implementation to store messages that meet one or more of the following criteria:
  - Message that is sent to a queue that does not exist.
  - Queue length limit exceeded.
  - Message length limit exceeded.
  - Message is rejected by another queue exchange.
  - Message reaches a threshold read counter number, because it is not consumed. Sometimes this is called a "back out queue".
  - Dead letter queue storing of these messages allows developers to look for common patterns and potential software problems.
  - Backout queue, on the other hand, is more of an application-level thing (at least in terms of MQ). When an MQ client cannot process the message for some reason, it can back it out for later processing (back to it's original queue).

## Durable / Non-Durable
- Subscribers to a topic destination have either durable and non-durable subscriptions. Durable subscriptions provide increased reliability at the cost of slower throughput.

**Durable:**
- Use for situations where you require guaranteed delivery, and ordering is not a concern, ie if we shut down the service the queue is persisted to disk/similar. And made available once the service comes back up. This is good as long as:
  - The volume of data will fit on disk and can be re-played in a timely fashion, I have seen implementations where the queue backlog took hours or days to replay.
  - The Message Queue message broker must persistently store the list of messages assigned to each durable subscription so that should the broker fail, the list is available after recovery.
  - Persistent messages for durable subscriptions are stored persistently, so that should a broker fail, the messages can still be delivered after recovery, when the corresponding consumer becomes active. By contrast, persistent messages for non-durable subscriptions are not stored persistently (should a broker fail, the corresponding consumer connection is lost and the message would never be delivered).
  - If the messages need to be delivered in a certain order then persistence can be bad. I have seen an implementation where status messages would arrive out of order and the consumer not checking the creation time.

**Non-durable:**
- Useful where the data can easily be re-played from a datastore.
- Does not require the ability to persist the data.
- Will come back online in constant time.
- + ~30% throughput performance.

# Domain Driven Design (DDD)

- Bounded Context – used to break down a large organization/domain model into manageable representative chunks, with explicitly defined shared objects:
 https://martinfowler.com/bliki/BoundedContext.html
- DDD divides up a large system into Bounded Contexts, each of which can have a unified model - essentially a way of structuring MultipleCanonicalModels.

# Value Object
- A Value Object is an immutable type that is distinguishable only by the state of its properties.

{% highlight Csharp %}
public class SomeValue
{
  public SomeValue(int value1, string value2)
  {
    this.Value1 = value1;
    this.Value2 = value2;
  }

  public int Value1 { get; private set; }
  public string Value2 { get; private set; }
}
{% endhighlight %}

# Functional Programming and F#

- Immutability – by default all objects are immutable, ie they are readonly once created.
- Immutable data makes the code predictable.
- Immutable data is easier to work with.
- Immutable data forces you to use a “transformational” approach.
- They are nearly always defined as having value based equality (If all of the fields are equal, the object is equal).
- Persistent data structures – To minimise memory usage when adding an object to a list F# takes a pointer to the original object and appends further elements, thus not copying the whole list.
- Null protection - The null value is not normally used in F# for values or variables. If a type is defined in some other .NET language, null is a possible value, and when you are interoperating with such types, your F# code might encounter null values.
- To interpret a null value that is obtained from a .NET method, use pattern matching if you can:
{% highlight Fsharp %}
match nextLine with
    		| null -> false
    		| inputString ->
        		match ParseDateTime inputString with
        		| Some(date) -> printfn "%s" (date.ToLocalTime().ToString())
        		| None -> printfn "Failed to parse the input."
        		true
{% endhighlight %}

# F# Async vs C# Task

- If you want to run multiple F# operations in parallel you should use Async.Parallel. If you use Async.Start you run the risk of unexpected consequences. Async gets run on the .Net thread pool, and should be used for long running requests eg: web requests to an external service, thus avoiding blocking the main thread.

## Functor

- A functor is a container of type a that, when subjected to a function that maps from a→b, yields a container of type b.


# References:

- Cracking the coding interview (book, ISBN 9780984782857) [http://www.crackingthecodinginterview.com](http://www.crackingthecodinginterview.com)
- Relational database concurrency - [Wikipedia Concurrency Control](https://en.wikipedia.org/wiki/Concurrency_control)
- Big O Notation - [Wikipedia Big O Notation](https://en.wikipedia.org/wiki/Big_O_notation)
- Domain Driven Design (DDD) - [Martin Fowler.com Bounded Context](https://martinfowler.com/bliki/BoundedContext.html)
- Mocks (Testing) - [medium.com what is mocking in testing](https://medium.com/@piraveenaparalogarajah/what-is-mocking-in-testing-d4b0f2dbe20a)
- Heap Sort (Sorting) - [Wikipedia Heapsort](https://en.wikipedia.org/wiki/Heapsort)
- Delegates - [docs.microsoft.com dotnet api Func<T,TResult> Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.func-2?view=netframework-4.8)
- DB Indexes [docs.microsoft.com sql relational-databases indexes clustered and nonclustered indexes](https://docs.microsoft.com/en-us/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)
- No Sql - [Wikipedia NoSQL](https://en.wikipedia.org/wiki/NoSQL)
- Object relational impedance mismatch - [Wikipedia Object Relational Impedance Mismatch](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)
- F# persistent-data-structures - [blog.mavnn.co.uk Persistent Data Structures](https://blog.mavnn.co.uk/persistent-data-structures)
- F# async - [microsoft.com fsharp tutorials asynchronous and concurrent programming](https://docs.microsoft.com/en-us/dotnet/fsharp/tutorials/asynchronous-and-concurrent-programming/async)
- F# Functor - [Stackoverflow what is a functor](https://stackoverflow.com/questions/2030863/in-functional-programming-what-is-a-functor)
- SOLID (O) Coffee Machine - [Stackify.com solid design open closed principle](https://stackify.com/solid-design-open-closed-principle)
- Async Await - [docs.microsoft.com csharp concepts async](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async)
- REST Architectural principles - [stackoverflow.com what exactly is restful programming](https://stackoverflow.com/questions/671118/what-exactly-is-restful-programming)
- REST versioning - [stackoverflow.com best practices for api versioning](https://stackoverflow.com/questions/389169/best-practices-for-api-versioning)
- Martin Fouler on versioning - [martinfowler.com enterpriseREST versioning](https://martinfowler.com/articles/enterpriseREST.html#versioning)
- Semantic versioning [https://semver.org](https://semver.org)
- MVC - [stackoverflow how to create custom compare attribute](https://stackoverflow.com/questions/10982012/how-to-create-custom-compare-attribute-for-view-model-properties)
- MVC - [tutorialsteacher.com MVC action-method-in-mvc](https://www.tutorialsteacher.com/mvc/action-method-in-mvc)
- Singleton - [CSharp In Depth Singleton](https://csharpindepth.com/articles/singleton)
- Generics - [Stackoverflow Cleanest Way To Write Retry Logic](https://stackoverflow.com/questions/1563191/cleanest-way-to-write-retry-logic)
- Generics - [Tutorials Teacher Generics](https://www.tutorialsteacher.com/csharp/constraints-in-generic-csharp)
- Auto Back Off - [docs.microsoft architecture patterns retry](https://docs.microsoft.com/en-us/azure/architecture/patterns/retry)
- [Microsoft retries exponential backoff](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
- [docs.microsoft.com dotnet wcf working with certificates](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates)
- Encryption - [Venafi.com Use cases of Symmetric and Asymmetric Encryption](https://www.venafi.com/blog/what-are-best-use-cases-symmetric-vs-asymmetric-encryption)
- Caching - [Michaels Coding Spot C# cache implementations ](https://michaelscodingspot.com/cache-implementations-in-csharp-net)
