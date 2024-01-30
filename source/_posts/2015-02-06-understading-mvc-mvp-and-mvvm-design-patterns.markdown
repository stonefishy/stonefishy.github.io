---

title: "Understading MVC, MVP and MVVM Design Patterns"
date: 2015-02-06 17:26:06 +0800
comments: true
categories: 设计模式
tags: [MVC, MVP, MVVM]
---

`MVC`, `MVP` and `MVVM` design patterns are most popular technologies in our web application. Many people don't understand what's difference among them, so do I. But I found a blog which explains the difference of three design patterns, and its' really good.    

To read original article, see [here](http://www.dotnet-tricks.com/Tutorial/designpatterns/2FMM060314-Understanding-MVC,-MVP-and-MVVM-Design-Patterns.html)    

There are three most popular MV-* design patterns: `MVC`, `MVP` and `MVVM`. These are widely used by the various technologies.   

<!-- more -->
### MVC Pattern

`MVC` stands for `Model-View-Controller`. It is a software design pattern which was introduced in 1970s. Also, `MVC` pattern forces a separation of concerns, it means domain model and controller logic are decoupled from user interface (view). As a result maintenance and testing of the application become simpler and easier.   

`MVC` design pattern splits an application into three main aspects: `Model`, `View` and `Controller`

![mvc-pattern](/assets/images/legacy/mvc-pattern.png)

#### Model
The Model represents a set of classes that describe the business logic i.e. business model as well as data access operations i.e. data model. It also defines business rules for data means how the data can be changed and manipulated.

#### View
The View represents the UI components like CSS, jQuery, html etc. It is only responsible for displaying the data that is received from the controller as the result. This also transforms the model(s) into UI.

#### Controller
The Controller is responsible to process incoming requests. It receives input from users via the View, then process the user's data with the help of Model and passing the results back to the View. Typically, it acts as the coordinator between the View and the Model.    

Today, this pattern is used by many popular framework like as `Ruby on Rails`, `Spring Framework`, `Apple iOS` Development and `ASP.NET MVC`.

### MVP Pattern
This pattern is similar to MVC pattern in which controller has been replaced by the presenter. This design pattern splits an application into three main aspects: `Model`, `View` and `Presenter`.   

![mvp-pattern](/assets/images/legacy/mvp-pattern.png)

#### Model
The Model represents a set of classes that describes the business logic and data. It also defines business rules for data means how the data can be changed and manipulated.

#### View
The View represents the UI components like CSS, jQuery, html etc. It is only responsible for displaying the data that is received from the presenter as the result. This also transforms the model(s) into UI.

### Presenter
The Presenter is responsible for handling all UI events on behalf of the view. This receive input from users via the View, then process the user's data with the help of Model and passing the results back to the View. Unlike view and controller, view and presenter are completely decoupled from each other’s and communicate to each other’s by an interface.   

Also, presenter does not manage the incoming request traffic as controller.   

This pattern is commonly used with `ASP.NET Web Forms` applications which require to create automated unit tests for their code-behind pages. This is also used with `windows forms`.   

##### Key Points about MVP Pattern:
- User interacts with the View.
- There is one-to-one relationship between View and Presenter means one View is mapped to only one Presenter.
- View has a reference to Presenter but View has not reference to Model.
- Provides two way communication between View and Presenter.

### MVVM Pattern
`MVVM` stands for `Model-View-View Model`. This pattern supports two-way data binding between view and View model. This enables automatic propagation of changes, within the state of view model to the View. Typically, the view model uses the observer pattern to notify changes in the view model to model.

![mvvm-pattern](/assets/images/legacy/mvvm-pattern.png)

#### Model
The Model represents a set of classes that describes the business logic and data. It also defines business rules for data means how the data can be changed and manipulated.

#### View
The View represents the UI components like CSS, jQuery, html etc. It is only responsible for displaying the data that is received from the controller as the result. This also transforms the model(s) into UI.

#### View Model
The View Model is responsible for exposing methods, commands, and other properties that helps to maintain the state of the view, manipulate the model as the result of actions on the view, and trigger events in the view itself.    

This pattern is commonly used by the `WPF`, `Silverlight`, `Caliburn`, `nRoute` etc.

##### Key Points about MVVM Pattern:
- User interacts with the View.
- There is many-to-one relationship between View and ViewModel means many View can be mapped to one ViewModel.
- View has a reference to ViewModel but View Model has no information about the View.
- Supports two-way data binding between View and ViewModel.




