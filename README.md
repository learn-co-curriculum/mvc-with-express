# MVC with Express

## Objectives

1. Understand the Components of MVC
2. Organize an Express application into MVC
3. Build a Javascript Controller
4. Attach an Action function to a Controller
5. Route an Request to a Controller Action
5. Integrate a Model into a Controller Action
6. Render a corresponding view for a controller action.

_A [video lecture](#video-lecture) covering this content is below the README_

## Following Along with Examples

To play with these examples, you have to:

`git clone github.com:learn-co-curriculum/mvc-with-express.git` into the Learn IDE or your environment. 

Then, `cd mvc-with-express` to work in the project folder. 

Run `npm install` too.

## What is Model-View-Controller?

As our applications grew increasingly complex, moving from simple I/O driven interfaces like the Command Line, to Graphical-User-Interfaces (GUI) and Web Applications, our code grew more complex in parralel. 

Instead of just managing a running process taking in text input, spitting out text output, we were suddenly managing windows, graphics, colors, and gestures like "Click" and "Drags", multiple-application states, and more. These requirements meant the way we architected our code needed to change.

Architecutre is how we organize the code in our application. It isn't speaking to a particular language, but rather, general, abstract, ideas of what works well in code. The principals we've discovered are not hard-and-fast rules, they are open to interpretation, and they will continue to evolve. Every developer has opinions on what is best and it's rarely a question of right or wrong or better or worse, it's mostly situational and personal.

One of the most timeless architectual patters for building complex applications is [Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). In essense, MVC is a pattern of seperating the code in our application into at least 3 major types of components, Models, Views, and Controllers. By decoupling the logic required to make an application, MVC allows for easier and conventional code organization, parralel development by multiple developers, and effecient code-reuse. 

![MVC in a Nutshell](https://cl.ly/nvZF/Image%202017-11-26%20at%204.20.45%20PM.png)

### Models

Models are traditionally the central component of the application's "[problem domain](https://en.wikipedia.org/wiki/Problem_domain)". Models represent the things in our application. In a "ToDo List" application, you would imagine having a thing in your application called a "ToDo List". Those lists would also have things called "Items", representing the things on the list. Models are generally implemented by creating a Class, with the instances being the individual manifestations of each unique concept.

Recently, Models are also being used to encapsulate lots of Database/Peristance logic. They are responsible for things like finding a particular user, finding the items in a todo list, and saving a new blog post. We see this in the Object-Relational-Mapper (ORM) design implemented into many Model layers in frameworks, like ActiveRecord in Rails.

Models don't care about the "Interface" that uses them - whether the applicatoin is being used by a person in a browser, an iPhone app, a command line, or via an API, Models are considered interface independent.

### Controllers

Controllers are sort of like traffic cops, or brokers, taking input or data from one place, generally the person using our application, sending it to another place, generally our models, and then deciding what to do next. Controllers manage the relationship between the interface that person is using for our application, the models that contain the domain logic, and the views that present data back to the person in the form of something they can see, whether it plain text as in a command-line or HTML in the form of a web application.

### Views

Views are responsible for presenting the end result of an interaction between a person and our application. Views are basically "how things look." In a web applications, views are our HTML templates and the final HTML we deliver to the browser. They know the littlest about the rest of the code and are generally devoid of much application logic besides how things should look.

### MVC Metaphor - A Restaurant 

Sometimes its useful to think of architectual patterns in terms of metaphors. MVC could be thought of as a restaurant, where every roll in the restaurant has a discrete responsibility.

Models are like Chefs. The most important part of the restaurant, they define the meals served. They know everything there is to know about cooking. They don't care who is eating or how the food gets to them, take out or dining in. They just make food.

Controllers are like Waiters. Nothing works without them. They take orders from people, give those orders to chefs, and coordinate the travel of the meal from kitchen to table. 

Views are...well, like the table or plate itself. Maybe [Saucier](https://en.wikipedia.org/wiki/Saucier) is a better term, but they are responsible for making sure everything looks good, the decor of the restaurant, the table and silverware, and perhaps even the platting.

## MVC in Express

Out of the box, Express is not an MVC Framework. But we're going to turn it into one so that when you do learn an MVC Framework, you'll understand that patterns and the components.

First, let's talk about a general MVC Express Application Structure

```
express-todomvc/
- bin/
- config/
- controllers/
- db/
- models/
- public/
- test/
- views/
- app.js
```

This is the general folder structure we'll use for our MVC Express applications.

### `bin` directory

Inside the `bin` directory we're going to put any of our scripts that execute the application. Generally we'll have a `bin/www` javascript file that starts up the web application server. We might also have a `bin/console` javascript file that loads our environment and application code and provides a Node console for us to play with it.

### `config` directory

Inside the `config` directory we will have anything that configures the behavoir of the application. The most important file in there is going to be `config/db.js` that loads our Database connection.

### `controllers` directory

You guessed it, we will put all of our controllers inside `controllers`, naming them in plural for the resource ("thing") they deal with, like `TodoListsController.js` for dealing with `TodoLists` or a `SiteController.js` for dealing with the main pages in our site, like a homepage or an about page. We use [PascalCase](https://en.wikipedia.org/wiki/PascalCase) for Controller naming.

### `db` directory

Our actual database files will go here. We also might put database migration files in this directory, but we're not going to deal too much with that yet.

### `models` directory

You guessed again, in the `models` directory we put models. Models are convetionally named in singular in PascalCase, so things like a `TodoList.js` or a `User.js`.

### `public` directory

Any asset that can be served without any interpretation, like `stylesheets`, or `images`, or even client-side `javascripts` go in here. In fact, it's common to have a directory for each of those.

### `test` directory.

Yep, our tests go in there.

### `views` directory.

Our views, organized by the controller that generally renders them, so with a `SiteController`, we would put the views relating to the about page in `site/about.ejs` or for the homepage, `site/index.ejs`, or for a `TodoListsController` we'd have `todo_lists/index.ejs` and `todo_lists/new.ejs`, where the folders are named in [snake_case](https://en.wikipedia.org/wiki/Snake_case).

### `app.js`

This is our main application file, defining the `app` constant from Express and generally handling our main routing responsibility, though you could separate that out into `config/routes.js` if it was very big.

### A Note About Convetions

Outlined above is just one interpretation that we are going to use for MVC with Express. It is possible to organize an MVC application in 100 different ways with 100 different conventions on how to name your files, where to put different components, etc. This is just the one we're going to use, it's good for teaching purposes, it's conventional and mirrors the Ruby on Rails framework. It isn't the best, but it's good enough. Learn one convention, then you can learn others. And don't listen to anyone telling you "that's not how to do MVC." There's no one right way to anything in life, why would programming be any different?

## Controllers in Express

In MVC Web Applications, the first thing to understand is the interaction between an HTTP Request and our application, specifically, the controllers.

A controller in our application is just a regular javascript object. We might have a `SiteController` defined in `controllers/SiteController.js`.

```js
const SiteController = {}

SiteController.Index = function(req, resp){
  resp.send("This is the Home Page")
}

SiteController.About = function(req, resp){
  resp.send("This is the About Page")
}

module.exports = SiteController
```

The first line defines the contoller constant as a POJO (Plain Old Javascript Object), `SiteController`. Each HTTP request this controller is intended to deal with will be represented as a function attached to the `SiteController`. We call these functions "actions" in the context of MVC. The controller `SiteController` has two actions, `Index` and `About`, each of which is a function intended to be an Express route handler, thus accepting the two arguments, `req` and `resp`.

We group related HTTP requests together in the form a contoller and the individual the functions that will handle each request.

Once our controller is defined, we need to make sure that the appropriate HTTP requests and URL are routed to these actions.

In `app.js` for this example we'd see:

```js
const SiteController = require("./controllers/SiteController.js")

app.get("/", SiteController.Index)
app.get("/about", SiteController.About)
```

We are passing the actions of the controller, into the app to become the handlers for those requests. That is all there is to connecting your Express routes to controller actions.

1. Create the controller object in a file in `controllers`. Don't forget to export the object with `module.exports`.
2. Attach functions to the object to act as handlers for each HTTP request.
3. Require the controller in `app.js`.
4. Draw your routes, passing each request to a handler function that is the controller's action.

## Rendering Views in a Controller Action

Now that we know how to define a controller, build actions onto it as express handler functions, and route HTTP requests to a controller action, let's just make sure we can render views in our controller actions.

What's nice about MVC and most architectual patterns, is that once you get the concept and the crucial implementation change, the rest is generally the same.

A controller action is just a function defined on an object meant to be an express handler. Once defined, that function behaves just like any express handler, it accepts a `req` and `resp` argument and must ultimately send a response back. Whether that function is defined on a controller or passed to the express route directly doesn't matter.

Rendering a view in a controller action is the same as rendering a view directly in the route handler, just use `resp.render` and all the same rules and logic apply.

```js
const SiteController = {}

SiteController.Index = function(req, resp){
  resp.render("site/index")
}

SiteController.About = function(req, resp){
  const welcomeText = "Welcome to the About Page!"
  resp.render("site/about", {welcomeText: welcomeText})
}

module.exports = SiteController
```

It is a convention to have our views for a controller live within a subdirectory in `views` named after the controller. `SiteController` would generally render views in `views/site`. `TodoListsController` might render views in `views/todo_lists`, but you're going to be explicit with your render paths relative to `views`, so just make sure it's an accurate path to the EJS template you intended.

## Integarting Models into Controllers



## Video Lecture

<iframe width="560" height="315" src="https://www.youtube.com/embed/OovkL82Jnx8?rel=0&modestbranding=1" frameborder="0" allowfullscreen></iframe><p><a href="https://youtu.be/OovkL82Jnx8">Intro to Express</a></p>

[Slides](https://docs.google.com/presentation/d/1cqqONyI0s3dnwzKmK34MMvQmh31mQrZ0mj7Pw_c-PBg/edit#slide=id.g242ae760d7_0_47)

## Resources 

* [Templating with EJS](https://scotch.io/tutorials/use-ejs-to-template-your-node-application)
