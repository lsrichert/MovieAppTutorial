# ASP.NET WEB APP TUTORIAL W/ VISUAL STUDIO

# PART ONE - GET STARTED
Create a web app
From Visual Studio, select File > New > Project.

File > New > Project

Complete the New Project dialog:

In the left pane, tap .NET Core
In the center pane, tap ASP.NET Core Web Application (.NET Core)
Name the project "MvcMovie" (It's important to name the project "MvcMovie" so when you copy code, the namespace will match.)
Tap OK
New project dialog, .Net core in left pane, ASP.NET Core web 

Complete the New ASP.NET Core Web Application (.NET Core) - MvcMovie dialog:

In the version selector drop-down box select ASP.NET Core 2.1
Select Web Application(Model-View-Controller)
Tap OK.
New project dialog, .Net core in left pane, ASP.NET Core web 

Visual Studio used a default template for the MVC project you just created. You have a working app right now by entering a project name and selecting a few options. This is a basic starter project, and it's a good place to start,

Tap F5 to run the app in debug mode or Ctrl-F5 in non-debug mode.

running app

Visual Studio starts IIS Express and runs your app. Notice that the address bar shows localhost:port# and not something like example.com. That's because localhost is the standard hostname for your local computer. When Visual Studio creates a web project, a random port is used for the web server. In the image above, the port number is 5000. The URL in the browser shows localhost:5000. When you run the app, you'll see a different port number.
Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes. Many developers prefer to use non-debug mode to quickly launch the app and view changes.
You can launch the app in debug or non-debug mode from the Debug menu item:
Debug menu

You can debug the app by tapping the IIS Express button
IIS Express

The default template gives you working Home, About and Contact links. The browser image above doesn't show these links. Depending on the size of your browser, you might need to click the navigation icon to show them.

navigation icon in upper right

If you were running in debug mode, tap Shift-F5 to stop debugging.

In the next part of this tutorial, we'll learn about MVC and start writing some code.

# PART TWO - ADD A CONTROLLER
The Model-View-Controller (MVC) architectural pattern separates an app into three main components: Model, View, and Controller. The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps. MVC-based apps contain:

Models: Classes that represent the data of the app. The model classes use validation logic to enforce business rules for that data. Typically, model objects retrieve and store model state in a database. In this tutorial, a Movie model retrieves movie data from a database, provides it to the view or updates it. Updated data is written to a database.

Views: Views are the components that display the app's user interface (UI). Generally, this UI displays the model data.

Controllers: Classes that handle browser requests. They retrieve model data and call view templates that return a response. In an MVC app, the view only displays information; the controller handles and responds to user input and interaction. For example, the controller handles route data and query-string values, and passes these values to the model. The model might use these values to query the database. For example, http://localhost:1234/Home/About has route data of Home (the controller) and About (the action method to call on the home controller). http://localhost:1234/Movies/Edit/5 is a request to edit the movie with ID=5 using the movie controller. We'll talk about route data later in the tutorial.

The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements. The pattern specifies where each kind of logic should be located in the app. The UI logic belongs in the view. Input logic belongs in the controller. Business logic belongs in the model. This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another. For example, you can work on the view code without depending on the business logic code.

We cover these concepts in this tutorial series and show you how to use them to build a movie app. The MVC project contains folders for the Controllers and Views.

In Solution Explorer, right-click Controllers > Add > New Item
Contextual menu

Select Controller Class
In the Add New Item dialog, enter HelloWorldController.
Add MVC controller and name it

Replace the contents of Controllers/HelloWorldController.cs with the following:

C#

Copy
using Microsoft.AspNetCore.Mvc;
using System.Text.Encodings.Web;

namespace MvcMovie.Controllers
{
    public class HelloWorldController : Controller
    {
        // 
        // GET: /HelloWorld/

        public string Index()
        {
            return "This is my default action...";
        }

        // 
        // GET: /HelloWorld/Welcome/ 

        public string Welcome()
        {
            return "This is the Welcome action method...";
        }
    }
}
Every public method in a controller is callable as an HTTP endpoint. In the sample above, both methods return a string. Note the comments preceding each method.

An HTTP endpoint is a targetable URL in the web application, such as http://localhost:1234/HelloWorld, and combines the protocol used: HTTP, the network location of the web server (including the TCP port): localhost:1234 and the target URI HelloWorld.

The first comment states this is an HTTP GET method that's invoked by appending "/HelloWorld/" to the base URL. The second comment specifies an HTTP GET method that's invoked by appending "/HelloWorld/Welcome/" to the URL. Later on in the tutorial you'll use the scaffolding engine to generate HTTP POST methods.

Run the app in non-debug mode and append "HelloWorld" to the path in the address bar. The Index method returns a string.

Browser window showing an application response of This is my default action

MVC invokes controller classes (and the action methods within them) depending on the incoming URL. The default URL routing logic used by MVC uses a format like this to determine what code to invoke:

/[Controller]/[ActionName]/[Parameters]

You set the format for routing in the Configure method in Startup.cs file.

C#

Copy
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
When you run the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.

The first URL segment determines the controller class to run. So localhost:xxxx/HelloWorld maps to the HelloWorldController class. The second part of the URL segment determines the action method on the class. So localhost:xxxx/HelloWorld/Index would cause the Index method of the HelloWorldController class to run. Notice that you only had to browse to localhost:xxxx/HelloWorld and the Index method was called by default. This is because Index is the default method that will be called on a controller if a method name isn't explicitly specified. The third part of the URL segment ( id) is for route data. You'll see route data later on in this tutorial.

Browse to http://localhost:xxxx/HelloWorld/Welcome. The Welcome method runs and returns the string "This is the Welcome action method...". For this URL, the controller is HelloWorld and Welcome is the action method. You haven't used the [Parameters] part of the URL yet.

Browser window showing an application response of This is the Welcome action method

Modify the code to pass some parameter information from the URL to the controller. For example, /HelloWorld/Welcome?name=Rick&numtimes=4. Change the Welcome method to include two parameters as shown in the following code.

C#

Copy
// GET: /HelloWorld/Welcome/ 
// Requires using System.Text.Encodings.Web;
public string Welcome(string name, int numTimes = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, NumTimes is: {numTimes}");
}
The preceding code:

Uses the C# optional-parameter feature to indicate that the numTimes parameter defaults to 1 if no value is passed for that parameter.
UsesHtmlEncoder.Default.Encode to protect the app from malicious input (namely JavaScript).
Uses Interpolated Strings.
Run your app and browse to:

http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4

(Replace xxxx with your port number.) You can try different values for name and numtimes in the URL. The MVC model binding system automatically maps the named parameters from the query string in the address bar to parameters in your method. See Model Binding for more information.

Browser window showing an application response of Hello Rick, NumTimes is: 4

In the image above, the URL segment (Parameters) isn't used, the name and numTimes parameters are passed as query strings. The ? (question mark) in the above URL is a separator, and the query strings follow. The & character separates query strings.

Replace the Welcome method with the following code:

C#

Copy
public string Welcome(string name, int ID = 1)
{
    return HtmlEncoder.Default.Encode($"Hello {name}, ID: {ID}");
}
Run the app and enter the following URL: http://localhost:xxx/HelloWorld/Welcome/3?name=Rick

Browser window showing an application response of Hello Rick, ID: 3

This time the third URL segment matched the route parameter id. The Welcome method contains a parameter id that matched the URL template in the MapRoute method. The trailing ? (in id?) indicates the id parameter is optional.

C#

Copy
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work. The controller is returning HTML directly. Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain. Instead you typically use a separate Razor view template file to help generate the HTML response. You do that in the next tutorial.

In Visual Studio, in non-debug mode (Ctrl+F5), you don't need to build the app after changing code. Just save the file, refresh your browser and you can see the changes.

#PART THREE - ADD A VIEW
In this section you modify the HelloWorldController class to use Razor view template files to cleanly encapsulate the process of generating HTML responses to a client.

You create a view template file using Razor. Razor-based view templates have a .cshtml file extension. They provide an elegant way to create HTML output using C#.

Currently the Index method returns a string with a message that's hard-coded in the controller class. In the HelloWorldController class, replace the Index method with the following code:

C#

Copy
public IActionResult Index()
{
    return View();
}
The preceding code returns a View object. It uses a view template to generate an HTML response to the browser. Controller methods (also known as action methods) such as the Index method above, generally return an IActionResult (or a class derived from ActionResult), not a type like string.

Right click on the Views folder, and then Add > New Folder and name the folder HelloWorld.

Right click on the Views/HelloWorld folder, and then Add > New Item.

In the Add New Item - MvcMovie dialog

In the search box in the upper-right, enter view

Tap Razor View

In the Name box, change the name if necessary to Index.cshtml.

Tap Add

Add New Item dialog

Replace the contents of the Views/HelloWorld/Index.cshtml Razor view file with the following:

HTML

Copy
@{
    ViewData["Title"] = "Index";
}

<h2>Index</h2>

<p>Hello from our View Template!</p>
Navigate to http://localhost:xxxx/HelloWorld. The Index method in the HelloWorldController didn't do much; it ran the statement return View();, which specified that the method should use a view template file to render a response to the browser. Because you didn't explicitly specify the name of the view template file, MVC defaulted to using the Index.cshtml view file in the /Views/HelloWorld folder. The image below shows the string "Hello from our View Template!" hard-coded in the view.

Browser window

If your browser window is small (for example on a mobile device), you might need to toggle (tap) the Bootstrap navigation button in the upper right to see the Home, About, and Contact links.

Browser window highlighting the Bootstrap navigation button

Changing views and layout pages
Tap the menu links (MvcMovie, Home, About). Each page shows the same menu layout. The menu layout is implemented in the Views/Shared/_Layout.cshtml file. Open the Views/Shared/_Layout.cshtml file.

Layout templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site. Find the @RenderBody() line. RenderBody is a placeholder where all the view-specific pages you create show up, wrapped in the layout page. For example, if you select the About link, the Views/Home/About.cshtml view is rendered inside the RenderBody method.

Change the title and menu link in the layout file
In the title element, change MvcMovie to Movie App. Change the anchor text in the layout template from MvcMovie to Movie App and the controller from Home to Movies as highlighted below:

HTML

Copy
@inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - Movie App</title>

    <environment names="Development">
        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </environment>
    <environment names="Staging,Production">
        <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
              asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
              asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
        <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
    </environment>
    @Html.Raw(JavaScriptSnippet.FullScript)
</head>
<body>
    <nav class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a asp-area="" asp-controller="Movies" asp-action="Index" class="navbar-brand">Movie App</a>
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li><a asp-area="" asp-controller="Home" asp-action="Index">Home</a></li>
                    <li><a asp-area="" asp-controller="Home" asp-action="About">About</a></li>
                    <li><a asp-area="" asp-controller="Home" asp-action="Contact">Contact</a></li>
                </ul>
            </div>
        </div>
    </nav>
    <div class="container body-content">
        @RenderBody()
        <hr />
        <footer>
            <p>&copy; 2017 - MvcMovie</p>
        </footer>
    </div>

    <environment names="Development">
        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    <environment names="Staging,Production">
        <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.2.0.min.js"
                asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
                asp-fallback-test="window.jQuery"
                crossorigin="anonymous"
                integrity="sha384-K+ctZQ+LL8q6tP7I94W+qzQsfRV2a+AfHIi9k8z8l9ggpc8X+Ytst4yBo/hH+8Fk">
        </script>
        <script src="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/bootstrap.min.js"
                asp-fallback-src="~/lib/bootstrap/dist/js/bootstrap.min.js"
                asp-fallback-test="window.jQuery && window.jQuery.fn && window.jQuery.fn.modal"
                crossorigin="anonymous"
                integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa">
        </script>
        <script src="~/js/site.min.js" asp-append-version="true"></script>
    </environment>

    @RenderSection("Scripts", required: false)
</body>
</html>
Warning

We haven't implemented the Movies controller yet, so if you click on that link, you'll get a 404 (Not found) error.

Save your changes and tap the About link. Notice how the title on the browser tab now displays About - Movie App instead of About - Mvc Movie:

About tab

Tap the Contact link and notice that the title and anchor text also display Movie App. We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.

Examine the Views/_ViewStart.cshtml file:

HTML

Copy
@{
    Layout = "_Layout";
}
The Views/_ViewStart.cshtml file brings in the Views/Shared/_Layout.cshtml file to each view. You can use the Layout property to set a different layout view, or set it to null so no layout file will be used.

Change the title of the Index view.

Open Views/HelloWorld/Index.cshtml. There are two places to make a change:

The text that appears in the title of the browser.
The secondary header (<h2> element).
You'll make them slightly different so you can see which bit of code changes which part of the app.

HTML

Copy
@{
    ViewData["Title"] = "Movie List";
}

<h2>My Movie List</h2>

<p>Hello from our View Template!</p>
ViewData["Title"] = "Movie List"; in the code above sets the Title property of the ViewData dictionary to "Movie List". The Title property is used in the <title> HTML element in the layout page:

HTML

Copy
<title>@ViewData["Title"] - Movie App</title>
Save your change and navigate to http://localhost:xxxx/HelloWorld. Notice that the browser title, the primary heading, and the secondary headings have changed. (If you don't see changes in the browser, you might be viewing cached content. Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with ViewData["Title"] we set in the Index.cshtml view template and the additional "- Movie App" added in the layout file.

Also notice how the content in the Index.cshtml view template was merged with the Views/Shared/_Layout.cshtml view template and a single HTML response was sent to the browser. Layout templates make it really easy to make changes that apply across all of the pages in your application. To learn more see Layout.

Movie List view

Our little bit of "data" (in this case the "Hello from our View Template!" message) is hard-coded, though. The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.

Passing Data from the Controller to the View
Controller actions are invoked in response to an incoming URL request. A controller class is where you write the code that handles the incoming browser requests. The controller retrieves data from a data source and decides what type of response to send back to the browser. View templates can be used from a controller to generate and format an HTML response to the browser.

Controllers are responsible for providing the data required in order for a view template to render a response. A best practice: View templates should not perform business logic or interact with a database directly. Rather, a view template should work only with the data that's provided to it by the controller. Maintaining this "separation of concerns" helps keep your code clean, testable, and maintainable.

Currently, the Welcome method in the HelloWorldController class takes a name and a ID parameter and then outputs the values directly to the browser. Rather than have the controller render this response as a string, change the controller to use a view template instead. The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response. Do this by having the controller put the dynamic data (parameters) that the view template needs in a ViewData dictionary that the view template can then access.

Return to the HelloWorldController.cs file and change the Welcome method to add a Message and NumTimes value to the ViewData dictionary. The ViewData dictionary is a dynamic object, which means you can put whatever you want in to it; the ViewData object has no defined properties until you put something inside it. The MVC model binding system automatically maps the named parameters (name and numTimes) from the query string in the address bar to parameters in your method. The complete HelloWorldController.cs file looks like this:

C#

Copy
using Microsoft.AspNetCore.Mvc;
using System.Text.Encodings.Web;

namespace MvcMovie.Controllers
{
    public class HelloWorldController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

        public IActionResult Welcome(string name, int numTimes = 1)
        {
            ViewData["Message"] = "Hello " + name;
            ViewData["NumTimes"] = numTimes;

            return View();
        }
    }
}
The ViewData dictionary object contains data that will be passed to the view.

Create a Welcome view template named Views/HelloWorld/Welcome.cshtml.

You'll create a loop in the Welcome.cshtml view template that displays "Hello" NumTimes. Replace the contents of Views/HelloWorld/Welcome.cshtml with the following:

HTML

Copy
@{
    ViewData["Title"] = "Welcome";
}

<h2>Welcome</h2>

<ul>
    @for (int i = 0; i < (int)ViewData["NumTimes"]; i++)
    {
        <li>@ViewData["Message"]</li>
    }
</ul>
Save your changes and browse to the following URL:

http://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4

Data is taken from the URL and passed to the controller using the MVC model binder . The controller packages the data into a ViewData dictionary and passes that object to the view. The view then renders the data as HTML to the browser.

About view showing a Welcome label and the phrase Hello Rick shown four times

In the sample above, we used the ViewData dictionary to pass data from the controller to a view. Later in the tutorial, we will use a view model to pass data from a controller to a view. The view model approach to passing data is generally much preferred over the ViewData dictionary approach. See ViewModel vs ViewData vs ViewBag vs TempData vs Session in MVC for more information.

Well, that was a kind of an "M" for model, but not the database kind. Let's take what we've learned and create a database of movies.

# PART FOUR - ADD A MODEL
In this section, you'll add some classes for managing movies in a database. These classes will be the "Model" part of the MVC app.

You use these classes with Entity Framework Core (EF Core) to work with a database. EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write. EF Core supports many database engines.

The model classes you'll create are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core. They just define the properties of the data that will be stored in the database.

In this tutorial you'll write the model classes first, and EF Core will create the database. An alternate approach not covered here is to generate model classes from an already-existing database. For information about that approach, see ASP.NET Core - Existing Database.

Add a data model class
Right-click the Models folder > Add > Class. Name the class Movie and add the following properties:

C#

Copy
using System;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int ID { get; set; }
        public string Title { get; set; }
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
The ID field is required by the database for the primary key.

Build the project to verify you don't have any errors. You now have a Model in your MVC app.

Scaffolding a controller
In Solution Explorer, right-click the Controllers folder > Add > New Scaffolded Item.

view of above step

In the Add Scaffold dialog, tap MVC Controller with views, using Entity Framework > Add.

Add Scaffold dialog

Complete the Add Controller dialog:

Model class: Movie (MvcMovie.Models)
Data context class: Select the + icon and add the default MvcMovie.Models.MvcMovieContext
Add Data context

Views: Keep the default of each option checked
Controller name: Keep the default MoviesController
Tap Add
Add Controller dialog

Visual Studio creates:

An Entity Framework Core database context class (Data/MvcMovieContext.cs)
A movies controller (Controllers/MoviesController.cs)
Razor view files for Create, Delete, Details, Edit, and Index pages (Views/Movies/*.cshtml)
The automatic creation of the database context and CRUD (create, read, update, and delete) action methods and views is known as scaffolding. You'll soon have a fully functional web application that lets you manage a movie database.

If you run the app and click on the Mvc Movie link, you get an error similar to the following:

error

Copy
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString 
You need to create the database, and you'll use the EF Core Migrations feature to do that. Migrations lets you create a database that matches your data model and update the database schema when your data model changes.

Add EF tooling and perform initial migration
In this section you'll use the Package Manager Console (PMC) to:

Add the Entity Framework Core Tools package. This package is required to add migrations and update the database.
Add an initial migration.
Update the database with the initial migration.
From the Tools menu, select NuGet Package Manager > Package Manager Console.

PMC menu

In the PMC, enter the following commands:

PMC

Copy
Add-Migration Initial
Update-Database
Ignore the following error message, we fix it in the next tutorial:

Microsoft.EntityFrameworkCore.Model.Validation[30000]
No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'.

The Add-Migration command creates code to create the initial database schema. The schema is based on the model specified in the DbContext(In the Data/MvcMovieContext.cs file). The Initial argument is used to name the migrations. You can use any name, but by convention you choose a name that describes the migration. See Introduction to migrations for more information.

The Update-Database command runs the Up method in the Migrations/<time-stamp>_Initial.cs file, which creates the database.

You can perform the preceeding steps using the command-line interface (CLI) rather than the PMC:

Add EF Core tooling to the .csproj file.

Run the following commands from the console (in the project directory):

console

Copy
dotnet ef migrations add Initial
dotnet ef database update
If you run the app and get the error:

text

Copy
SqlException: Cannot open database "Movie" requested by the login.
The login failed.
Login failed for user 'user name'.
You probably have not run dotnet ef database update.

Test the app
Run the app and tap the Mvc Movie link.

Tap the Create New link and create a movie.

Create view with fields for genre, price, release date, and title

You may not be able to enter decimal points or commas in the Price field. To support jQuery validation for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app. See https://github.com/aspnet/Docs/issues/4076 and Additional resources for more information. For now, just enter whole numbers like 10.


In some locales you need to specify the date format. See the highlighted code below.
C#

Copy
using System;
using System.ComponentModel.DataAnnotations;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int ID { get; set; }
        public string Title { get; set; }
        [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }
        public decimal Price { get; set; }
    }
}
We'll talk about DataAnnotations later in the tutorial.

Tapping Create causes the form to be posted to the server, where the movie information is saved in a database. The app redirects to the /Movies URL, where the newly created movie information is displayed.

Movies view showing newly created movie listing

Create a couple more movie entries. Try the Edit, Details, and Delete links, which are all functional.

C#

Copy
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
    });


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    services.AddDbContext<MvcMovieContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
}
The highlighted code above shows the movie database context being added to the Dependency Injection container (In the Startup.cs file). services.AddDbContext<MvcMovieContext>(options => specifies the database to use and the connection string. => is a lambda operator.

Open the Controllers/MoviesController.cs file and examine the constructor:

C#

Copy
public class MoviesController : Controller
{
    private readonly MvcMovieContext _context;

    public MoviesController(MvcMovieContext context)
    {
        _context = context;
    }
The constructor uses Dependency Injection to inject the database context (MvcMovieContext) into the controller. The database context is used in each of the CRUD methods in the controller.


Strongly typed models and the @model keyword
Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the ViewData dictionary. The ViewData dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.

MVC also provides the ability to pass strongly typed model objects to a view. This strongly typed approach enables better compile-time checking of your code. The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the MoviesController class and views when it created the methods and views.

Examine the generated Details method in the Controllers/MoviesController.cs file:

C#

Copy
// GET: Movies/Details/5
public async Task<IActionResult> Details(int? id)
{
    if (id == null)
    {
        return NotFound();
    }

    var movie = await _context.Movie
        .FirstOrDefaultAsync(m => m.ID == id);
    if (movie == null)
    {
        return NotFound();
    }

    return View(movie);
}
The id parameter is generally passed as route data. For example http://localhost:5000/movies/details/1 sets:

The controller to the movies controller (the first URL segment).
The action to details (the second URL segment).
The id to 1 (the last URL segment).
You can also pass in the id with a query string as follows:

http://localhost:1234/movies/details?id=1

The id parameter is defined as a nullable type (int?) in case an ID value isn't provided.

A lambda expression is passed in to FirstOrDefaultAsync to select movie entities that match the route data or query string value.

C#

Copy
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.ID == id);
If a movie is found, an instance of the Movie model is passed to the Details view:

C#

Copy
return View(movie);
Examine the contents of the Views/Movies/Details.cshtml file:

HTML

Copy
@model MvcMovie.Models.Movie

@{
    ViewData["Title"] = "Details";
}

<h2>Details</h2>

<div>
    <h4>Movie</h4>
    <hr />
    <dl class="dl-horizontal">
        <dt>
            @Html.DisplayNameFor(model => model.Title)
        </dt>
        <dd>
            @Html.DisplayFor(model => model.Title)
        </dd>
        <dt>
            @Html.DisplayNameFor(model => model.ReleaseDate)
        </dt>
        <dd>
            @Html.DisplayFor(model => model.ReleaseDate)
        </dd>
        <dt>
            @Html.DisplayNameFor(model => model.Genre)
        </dt>
        <dd>
            @Html.DisplayFor(model => model.Genre)
        </dd>
        <dt>
            @Html.DisplayNameFor(model => model.Price)
        </dt>
        <dd>
            @Html.DisplayFor(model => model.Price)
        </dd>
    </dl>
</div>
<div>
    <a asp-action="Edit" asp-route-id="@Model.ID">Edit</a> |
    <a asp-action="Index">Back to List</a>
</div>
By including a @model statement at the top of the view file, you can specify the type of object that the view expects. When you created the movie controller, Visual Studio automatically included the following @model statement at the top of the Details.cshtml file:

HTML

Copy
@model MvcMovie.Models.Movie
This @model directive allows you to access the movie that the controller passed to the view by using a Model object that's strongly typed. For example, in the Details.cshtml view, the code passes each movie field to the DisplayNameFor and DisplayFor HTML Helpers with the strongly typed Model object. The Create and Edit methods and views also pass a Movie model object.

Examine the Index.cshtml view and the Index method in the Movies controller. Notice how the code creates a List object when it calls the View method. The code passes this Movies list from the Index action method to the view:

C#

Copy
// GET: Movies
public async Task<IActionResult> Index()
{
    return View(await _context.Movie.ToListAsync());
}
When you created the movies controller, scaffolding automatically included the following @model statement at the top of the Index.cshtml file:

HTML

Copy
@model IEnumerable<MvcMovie.Models.Movie>
The @model directive allows you to access the list of movies that the controller passed to the view by using a Model object that's strongly typed. For example, in the Index.cshtml view, the code loops through the movies with a foreach statement over the strongly typed Model object:

HTML

Copy
@model IEnumerable<MvcMovie.Models.Movie>

@{
    ViewData["Title"] = "Index";
}

<h2>Index</h2>

<p>
    <a asp-action="Create">Create New</a>
</p>
<table class="table">
    <thead>
        <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Title)
                </th>
                <th>
                    @Html.DisplayNameFor(model => model.ReleaseDate)
                </th>
                <th>
                    @Html.DisplayNameFor(model => model.Genre)
                </th>
                <th>
                    @Html.DisplayNameFor(model => model.Price)
                </th>
            <th></th>
        </tr>
    </thead>
    <tbody>
@foreach (var item in Model) {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Title)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.ReleaseDate)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Genre)
            </td>
            <td>
                @Html.DisplayFor(modelItem => item.Price)
            </td>
            <td>
                <a asp-action="Edit" asp-route-id="@item.ID">Edit</a> |
                <a asp-action="Details" asp-route-id="@item.ID">Details</a> |
                <a asp-action="Delete" asp-route-id="@item.ID">Delete</a>
            </td>
        </tr>
}
    </tbody>
</table>
Because the Model object is strongly typed (as an IEnumerable<Movie> object), each item in the loop is typed as Movie. Among other benefits, this means that you get compile-time checking of the code:

Intellisense contextual menu on a Model item listing the available properties for ID, Price, Release Date, and Title

# PART FIVE - WORK WITH SQL SERVER LOCAL DB
The MvcMovieContext object handles the task of connecting to the database and mapping Movie objects to database records. The database context is registered with the Dependency Injection container in the ConfigureServices method in the Startup.cs file:

C#

Copy
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        // This lambda determines whether user consent for non-essential cookies is needed for a given request.
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
    });


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    services.AddDbContext<MvcMovieContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MvcMovieContext")));
}
The ASP.NET Core Configuration system reads the ConnectionString. For local development, it gets the connection string from the appsettings.json file:

JSON

Copy
"ConnectionStrings": {
  "MvcMovieContext": "Server=(localdb)\\mssqllocaldb;Database=MvcMovieContext-2;Trusted_Connection=True;MultipleActiveResultSets=true"
}
When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server. See Configuration for more information.

SQL Server Express LocalDB
LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development. LocalDB starts on demand and runs in user mode, so there's no complex configuration. By default, LocalDB database creates "*.mdf" files in the C:/Users/<user> directory.

From the View menu, open SQL Server Object Explorer (SSOX).

View menu

Right click on the Movie table > View Designer

Contextual menu open on Movie table

Movie table open in Designer

Note the key icon next to ID. By default, EF will make a property named ID the primary key.

Right click on the Movie table > View Data

Contextual menu open on Movie table

Movie table open showing table data

Seed the database
Create a new class named SeedData in the Models folder. Replace the generated code with the following:

C#

Copy
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using System;
using System.Linq;

namespace MvcMovie.Models
{
    public static class SeedData
    {
        public static void Initialize(IServiceProvider serviceProvider)
        {
            using (var context = new MvcMovieContext(
                serviceProvider.GetRequiredService<DbContextOptions<MvcMovieContext>>()))
            {
                // Look for any movies.
                if (context.Movie.Any())
                {
                    return;   // DB has been seeded
                }

                context.Movie.AddRange(
                     new Movie
                     {
                         Title = "When Harry Met Sally",
                         ReleaseDate = DateTime.Parse("1989-1-11"),
                         Genre = "Romantic Comedy",
                         Price = 7.99M
                     },

                     new Movie
                     {
                         Title = "Ghostbusters ",
                         ReleaseDate = DateTime.Parse("1984-3-13"),
                         Genre = "Comedy",
                         Price = 8.99M
                     },

                     new Movie
                     {
                         Title = "Ghostbusters 2",
                         ReleaseDate = DateTime.Parse("1986-2-23"),
                         Genre = "Comedy",
                         Price = 9.99M
                     },

                   new Movie
                   {
                       Title = "Rio Bravo",
                       ReleaseDate = DateTime.Parse("1959-4-15"),
                       Genre = "Western",
                       Price = 3.99M
                   }
                );
                context.SaveChanges();
            }
        }
    }
}
If there are any movies in the DB, the seed initializer returns and no movies are added.

C#

Copy
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}

Add the seed initializer
Replace the contents of Program.cs with the following code:

C#

Copy
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using System;
using Microsoft.EntityFrameworkCore;
using MvcMovie.Models;
using MvcMovie;

namespace MvcMovie
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateWebHostBuilder(args).Build();

            using (var scope = host.Services.CreateScope())
            {
                var services = scope.ServiceProvider;

                try
                {
                    var context = services.GetRequiredService<MvcMovieContext>();
                    context.Database.Migrate();
                    SeedData.Initialize(services);
                }
                catch (Exception ex)
                {
                    var logger = services.GetRequiredService<ILogger<Program>>();
                    logger.LogError(ex, "An error occurred seeding the DB.");
                }
            }

            host.Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>();
    }
}
Test the app

Delete all the records in the DB. You can do this with the delete links in the browser or from SSOX.

Force the app to initialize (call the methods in the Startup class) so the seed method runs. To force initialization, IIS Express must be stopped and restarted. You can do this with any of the following approaches:

Right click the IIS Express system tray icon in the notification area and tap Exit or Stop Site

IIS Express system tray icon

Contextual menu

If you were running VS in non-debug mode, press F5 to run in debug mode
If you were running VS in debug mode, stop the debugger and press F5
The app shows the seeded data.

MVC Movie application open in Microsoft Edge showing movie data

# PART SIX - CONTROLLER METHODS AND VIEWS
We have a good start to the movie app, but the presentation isn't ideal. We don't want to see the time (12:00:00 AM in the image below) and ReleaseDate should be two words.

Index view: Release Date is one word (no space) and every movie release date shows a time of 12 AM

Open the Models/Movie.cs file and add the highlighted lines shown below:

C#

Copy
using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace MvcMovie.Models
{
    public class Movie
    {
        public int ID { get; set; }
        public string Title { get; set; }

        [Display(Name = "Release Date")]
        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }
        public string Genre { get; set; }

        [Column(TypeName = "decimal(18, 2)")]
        public decimal Price { get; set; }
    }
}
We cover DataAnnotations in the next tutorial. The Display attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate"). The DataType attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.

The [Column(TypeName = "decimal(18, 2)")] data annotation is required so Entity Framework Core can correctly map Price to currency in the database. For more information, see Data Types.

Browse to the Movies controller and hold the mouse pointer over an Edit link to see the target URL.

Browser window with mouse over the Edit link and a link Url of http://localhost:1234/Movies/Edit/5 is shown

The Edit, Details, and Delete links are generated by the Core MVC Anchor Tag Helper in the Views/Movies/Index.cshtml file.

HTML

Copy
        <a asp-action="Edit" asp-route-id="@item.ID">Edit</a> |
        <a asp-action="Details" asp-route-id="@item.ID">Details</a> |
        <a asp-action="Delete" asp-route-id="@item.ID">Delete</a>
    </td>
</tr>
Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files. In the code above, the AnchorTagHelper dynamically generates the HTML href attribute value from the controller action method and route id. You use View Source from your favorite browser or use the developer tools to examine the generated markup. A portion of the generated HTML is shown below:

HTML

Copy
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
Recall the format for routing set in the Startup.cs file:

C#

Copy
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
ASP.NET Core translates http://localhost:1234/Movies/Edit/4 into a request to the Edit action method of the Movies controller with the parameter Id of 4. (Controller methods are also known as action methods.)

Tag Helpers are one of the most popular new features in ASP.NET Core. See Additional resources for more information.

Open the Movies controller and examine the two Edit action methods. The following code shows the HTTP GET Edit method, which fetches the movie and populates the edit form generated by the Edit.cshtml Razor file.

C#

Copy
// GET: Movies/Edit/5
public async Task<IActionResult> Edit(int? id)
{
    if (id == null)
    {
        return NotFound();
    }

    var movie = await _context.Movie.FindAsync(id);
    if (movie == null)
    {
        return NotFound();
    }
    return View(movie);
}
The following code shows the HTTP POST Edit method, which processes the posted movie values:

C#

Copy
// POST: Movies/Edit/5
// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Edit(int id, [Bind("ID,Title,ReleaseDate,Genre,Price")] Movie movie)
{
    if (id != movie.ID)
    {
        return NotFound();
    }

    if (ModelState.IsValid)
    {
        try
        {
            _context.Update(movie);
            await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)
        {
            if (!MovieExists(movie.ID))
            {
                return NotFound();
            }
            else
            {
                throw;
            }
        }
        return RedirectToAction("Index");
    }
    return View(movie);
}
The [Bind] attribute is one way to protect against over-posting. You should only include properties in the [Bind] attribute that you want to change. See Protect your controller from over-posting for more information. ViewModels provide an alternative approach to prevent over-posting.

Notice the second Edit action method is preceded by the [HttpPost] attribute.

C#

Copy
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Edit(int id, [Bind("ID,Title,ReleaseDate,Genre,Price")] Movie movie)
{
    if (id != movie.ID)
    {
        return NotFound();
    }

    if (ModelState.IsValid)
    {
        try
        {
            _context.Update(movie);
            await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)
        {
            if (!MovieExists(movie.ID))
            {
                return NotFound();
            }
            else
            {
                throw;
            }
        }
        return RedirectToAction(nameof(Index));
    }
    return View(movie);
}
The HttpPost attribute specifies that this Edit method can be invoked only for POST requests. You could apply the [HttpGet] attribute to the first edit method, but that's not necessary because [HttpGet] is the default.

The ValidateAntiForgeryToken attribute is used to prevent forgery of a request and is paired up with an anti-forgery token generated in the edit view file (Views/Movies/Edit.cshtml). The edit view file generates the anti-forgery token with the Form Tag Helper.

HTML

Copy
<form asp-action="Edit">
The Form Tag Helper generates a hidden anti-forgery token that must match the [ValidateAntiForgeryToken] generated anti-forgery token in the Edit method of the Movies controller. For more information, see Anti-Request Forgery.

The HttpGet Edit method takes the movie ID parameter, looks up the movie using the Entity Framework SingleOrDefaultAsync method, and returns the selected movie to the Edit view. If a movie cannot be found, NotFound (HTTP 404) is returned.

C#

Copy
// GET: Movies/Edit/5
public async Task<IActionResult> Edit(int? id)
{
    if (id == null)
    {
        return NotFound();
    }

    var movie = await _context.Movie.FindAsync(id);
    if (movie == null)
    {
        return NotFound();
    }
    return View(movie);
}
When the scaffolding system created the Edit view, it examined the Movie class and created code to render <label> and <input> elements for each property of the class. The following example shows the Edit view that was generated by the Visual Studio scaffolding system:

HTML

Copy
@model MvcMovie.Models.Movie

@{
    ViewData["Title"] = "Edit";
}

<h2>Edit</h2>

<form asp-action="Edit">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <hr />
        <div asp-validation-summary="ModelOnly" class="text-danger"></div>
    <input type="hidden" asp-for="ID" />
        <div class="form-group">
            <label asp-for="Title" class="col-md-2 control-label"></label>
            <div class="col-md-10">
                <input asp-for="Title" class="form-control" />
                <span asp-validation-for="Title" class="text-danger"></span>
            </div>
        </div>
        <div class="form-group">
            <label asp-for="ReleaseDate" class="col-md-2 control-label"></label>
            <div class="col-md-10">
                <input asp-for="ReleaseDate" class="form-control" />
                <span asp-validation-for="ReleaseDate" class="text-danger"></span>
            </div>
        </div>
        <div class="form-group">
            <label asp-for="Genre" class="col-md-2 control-label"></label>
            <div class="col-md-10">
                <input asp-for="Genre" class="form-control" />
                <span asp-validation-for="Genre" class="text-danger"></span>
            </div>
        </div>
        <div class="form-group">
            <label asp-for="Price" class="col-md-2 control-label"></label>
            <div class="col-md-10">
                <input asp-for="Price" class="form-control" />
                <span asp-validation-for="Price" class="text-danger"></span>
            </div>
        </div>
        <div class="form-group">
            <div class="col-md-offset-2 col-md-10">
                <input type="submit" value="Save" class="btn btn-default" />
            </div>
        </div>
    </div>
</form>

<div>
    <a asp-action="Index">Back to List</a>
</div>

@section Scripts {
    @{await Html.RenderPartialAsync("_ValidationScriptsPartial");}
}
Notice how the view template has a @model MvcMovie.Models.Movie statement at the top of the file. @model MvcMovie.Models.Movie specifies that the view expects the model for the view template to be of type Movie.

The scaffolded code uses several Tag Helper methods to streamline the HTML markup. The - Label Tag Helper displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price"). The Input Tag Helper renders an HTML <input> element. The Validation Tag Helper displays any validation messages associated with that property.

Run the application and navigate to the /Movies URL. Click an Edit link. In the browser, view the source for the page. The generated HTML for the <form> element is shown below.

HTML

Copy
<form action="/Movies/Edit/7" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <hr />
        <div class="text-danger" />
        <input type="hidden" data-val="true" data-val-required="The ID field is required." id="ID" name="ID" value="7" />
        <div class="form-group">
            <label class="control-label col-md-2" for="Genre" />
            <div class="col-md-10">
                <input class="form-control" type="text" id="Genre" name="Genre" value="Western" />
                <span class="text-danger field-validation-valid" data-valmsg-for="Genre" data-valmsg-replace="true"></span>
            </div>
        </div>
        <div class="form-group">
            <label class="control-label col-md-2" for="Price" />
            <div class="col-md-10">
                <input class="form-control" type="text" data-val="true" data-val-number="The field Price must be a number." data-val-required="The Price field is required." id="Price" name="Price" value="3.99" />
                <span class="text-danger field-validation-valid" data-valmsg-for="Price" data-valmsg-replace="true"></span>
            </div>
        </div>
        <!-- Markup removed for brevity -->
        <div class="form-group">
            <div class="col-md-offset-2 col-md-10">
                <input type="submit" value="Save" class="btn btn-default" />
            </div>
        </div>
    </div>
    <input name="__RequestVerificationToken" type="hidden" value="CfDJ8Inyxgp63fRFqUePGvuI5jGZsloJu1L7X9le1gy7NCIlSduCRx9jDQClrV9pOTTmqUyXnJBXhmrjcUVDJyDUMm7-MF_9rK8aAZdRdlOri7FmKVkRe_2v5LIHGKFcTjPrWPYnc9AdSbomkiOSaTEg7RU" />
</form>
The <input> elements are in an HTML <form> element whose action attribute is set to post to the /Movies/Edit/id URL. The form data will be posted to the server when the Save button is clicked. The last line before the closing </form> element shows the hidden XSRF token generated by the Form Tag Helper.

Processing the POST Request
The following listing shows the [HttpPost] version of the Edit action method.

C#

Copy
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Edit(int id, [Bind("ID,Title,ReleaseDate,Genre,Price")] Movie movie)
{
    if (id != movie.ID)
    {
        return NotFound();
    }

    if (ModelState.IsValid)
    {
        try
        {
            _context.Update(movie);
            await _context.SaveChangesAsync();
        }
        catch (DbUpdateConcurrencyException)
        {
            if (!MovieExists(movie.ID))
            {
                return NotFound();
            }
            else
            {
                throw;
            }
        }
        return RedirectToAction(nameof(Index));
    }
    return View(movie);
}
The [ValidateAntiForgeryToken] attribute validates the hidden XSRF token generated by the anti-forgery token generator in the Form Tag Helper

The model binding system takes the posted form values and creates a Movie object that's passed as the movie parameter. The ModelState.IsValid method verifies that the data submitted in the form can be used to modify (edit or update) a Movie object. If the data is valid it's saved. The updated (edited) movie data is saved to the database by calling the SaveChangesAsync method of database context. After saving the data, the code redirects the user to the Index action method of the MoviesController class, which displays the movie collection, including the changes just made.

Before the form is posted to the server, client side validation checks any validation rules on the fields. If there are any validation errors, an error message is displayed and the form isn't posted. If JavaScript is disabled, you won't have client side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages. Later in the tutorial we examine Model Validation in more detail. The Validation Tag Helper in the Views/Movies/Edit.cshtml view template takes care of displaying appropriate error messages.

Edit view: An exception for an incorrect Price value of abc states that The field Price must be a number. An exception for an incorrect Release Date value of xyz states Please enter a valid date.

All the HttpGet methods in the movie controller follow a similar pattern. They get a movie object (or list of objects, in the case of Index), and pass the object (model) to the view. The Create method passes an empty movie object to the Create view. All the methods that create, edit, delete, or otherwise modify data do so in the [HttpPost] overload of the method. Modifying data in an HTTP GET method is a security risk. Modifying data in an HTTP GET method also violates HTTP best practices and the architectural REST pattern, which specifies that GET requests shouldn't change the state of your application. In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.

# PART SEVEN - 
