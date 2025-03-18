<div id="readme-top">
    <br/> 
    <p align="center"> 
        <img src="Images/MotoDesk.png" alt="Soho Okna Logo" style="width: 200px"> 
            <h2 align="center">MotoDeks - Car Workshop Visits Managament System
            </h2> 
    </p> 
</div>

<br>

## About the project

MotoDesk is a relatively simple application created for a sole proprietorship in the field of car mechanics. The app is designed to provide functionalities for managing customers, their cars, and visits, improving the overall efficiency of the workshop.

### Built With

[![.NET][.NetCsharp]][.NetCsharp-url]

### Modules

The first version of the application focuses on delivering the following functionalities:
- **Customer management** - allows the user to add, edit and remove the customer form the application's database 
- **Car management** -  similar to customer management, but related to cars associated with customers already present in the database. Each car must be assigned to a customer
- **Visit management** - enables all CRUD (Create, Read, Update, Delete) operations on visit records

<br>

Each module supports filtering, sorting, and viewing details of stored data.

Each module is a dedicated page for managing a specific type of data. For example, the Visits Management Page allows performing operations on visit records.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Application UI

The UI is built using a library with custom components based on the Windows 11 design.

<br>

![MotoDeskVisitsPage]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Example functionality - Adding a new visit

To showcase my approach to developing desktop applications, I will present the process of adding a new visit.

### Navigation

The user can navigate to one of the application modules by selecting an option from the navigation menu on the left side of the window. Here, all CRUD operations on visit records are available, as shown in the screenshot above.

Visits are listed using a custom **DataGrid** component, allowing filtering and sorting. By clicking "_Dodaj wizytę_", the user navigates to the Visit Adding subpage.

<br>

![MotoDeskAddVisitPage]

Upon navigating to the subpage, its name is displayed above the form using a **breadcrumbs** component, which is interactive – user can click on a specific part of the path to go back.

After filling out the form and submitting a new visit, the DataGrid in the previous view updates automatically.

### Code implementation

The project follows the **MVVM architecture**, which is a standard pattern for WPF applications. The development approach includes **Dependency Injection** and the **Strategy Design Pattern** (for service registration in the DI container).

Application is structured into separate projects within the Visual Studio solution, implementing the **Onion Architecture**. This approach improves scalability and flexibility (e.g. referencing application logic services through interfaces, preventing dependency on a single implementation).  

Once a visit is submitted, the **AddVisitAndNavigateBack** method (bound via the MVVM binding mechanism) is called. This method first validates whether all required fields are filled before proceeding with database access.

``` cs
[RelayCommand(CanExecute = nameof(CanSave))]
private void AddVisitAndNavigateBack()
{
    if (!CanSave)
        return;

    var visitToAdd = new Visit()
    {
        CustomerId = SelectedCustomer!.Id,
        BeginDate = DateOnly.FromDateTime(BeginDate!.Value),
        FinishDate = DateOnly.FromDateTime(FinishDate!.Value),
        VisitCost = decimal.Parse(VisitCost),
        Comment = Comment,
        IsFinished = false
    };

    _visitService.AddVisit(visitToAdd);

    _messenger.Send(new NewVisitAddedMessage());

    // Function of the navigation functionality
    NavigateBack();
}
```

The **_messenger** instance is part of the **CommunityToolkit.Mvvm** library, which provides a simple message bus functionality. Below is an example of how a new message type is implemented, how messages are received, and what happens when they are received:

``` cs
public class ExampleEmptyMessage
{
    // Empty message
}

// ...

_messenger.Register<ExampleEmptyMessage>(this, (_, _) => { FunctionToExecute(); });
```
Once the messenger object is initialized and subscribes to the message type, it will execute the provided function (**FunctionToExecute**) each time a message of type ExampleEmptyMessage is sent using:

``` cs 
_messenger.Send(new ExampleEmptyMessage());
```

By default, the registration uses **weak references**, meaning it may stop working if the subscriber is garbage collected.


The code below demonstrates how instances are initialized and how it is determined whether a visit can be saved:

``` cs
// ...

private readonly IMessenger _messenger;
private readonly IVisitService _visitService;

        public AddVisitPageViewModel(
            IVisitService visitService, 
            ICustomerService customerService, 
            INavigationService navigationService,
            IMessenger messenger
            )
        {
            _messenger = messenger;
            _visitService = visitService;
            
            // ...
        }

//  ...

private bool CanSave =>
SelectedCustomer != null &&
    BeginDate.HasValue &&
    FinishDate.HasValue &&
    decimal.TryParse(VisitCost, out _);
```


<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact
Jakub Nowak - [@LinkedIn Profile](https://www.linkedin.com/in/jakub-nowak-a245312b7/)
<br/> jakubszymonnowak@gmail.com

<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- MARKDOWN LINKS & IMAGES -->
[MotoDeskVisitsPage]: Images/MotoDeskVisitsPage.png
[MotoDeskAddVisitPage]: Images/MotoDeskAddVisitPage.png
[.NetCsharp]: https://img.shields.io/badge/-.NET%208.0%20%7C%20%20C%23%2012.0-blueviolet?style=for-the-badge
[.NetCsharp-url]: https://dotnet.microsoft.com/en-us/
