# dbgeo-simple-api
PostgreSQL Database API for maps and visualizations using PostGIS, NodeJS, and Express

# Leaflet with PostGIS, NodeJS, and Express

* * *

## Use NodeJS to create a Basic Database API

* * *

Imagine you want to make a web-based map from data you have stored in an in-house database. The dataset is really big, meaning you can't show it all at once or you will crash your browser, and you don't feel comfortable putting it onto CartoDB, like we went over in the previous session. There are a couple of ways to do this. One, you can use PHP, but that can be alot of work. Two, you could use NodeJS and set up your server to run JavaScript! Node will run on your server, meaning queries will be really fast, you can pass variables and data from the client to the server and back again, and you won't be exposing your credentials to the client. This exercise will set up a rudimentary service using Express, a NodeJS Framework, that you can expand to your own application.

#### Working with a PostGIS/NodeJS/Express/Leaflet Stack

_**What?**_ Set up a Leaflet map or D3 visualization to run from dynamically delivered data.
_**Why?**_ Allows for event-driven I/O and http calls. Allows for server-side JavaScript for database connections and front end integration.

_**Objectives of this Tutorial**_

In this tutorial, we will set up a web application that connects to a Postgres database, query that database and return the results of that query as a GeoJSON format. Then we will pass the data to the client to be viewed in a Leaflet webmap.

This arrangement will allow us to store data in Postgres and run spatial queries on the data using PostGIS, then passes on the dataset in JSON form to our client for display. Think of it as a rudimentary API.

* * *

### 1\. Load Data into Postgres/PostGIS

First, we need data to map. Let's use the Cambridge Coffee Shop dataset that we've used in previous exercises. Instead of using CartoDB, we are going to use our own installation of PostgreSQL.

#### Using QGIS

Open up QGIS and connect to your local instance of Postgres.

Load **cambridge_coffee_shops** into PostGIS. The recommended route for this is to load your data into QGIS, save a copy of your dataset in WGS84 coordinate system, the use the **Database Manager** to import your data into PostGIS.

Create a table in your database called **cambridge_coffee_shops**. The following will be built off of this database table as an example.

For more detailed instructions on loading data, see the Setting up PostGIS tutorial. **_Coming Soon._**

Note here the credentials of your database. You are going to need the database credentials to connect to your database in later steps. If you are using localhost, use your local credentials, if using a database server, use those credentials.

### 2\. Install NodeJS

NodeJS is an event-driven I/O server-side JavaScript environment that allows for JavaScript to operate on the server. In practice, for us, when you install on your development or production server, it allows a more seamless interface with your backend, allowing for connections to databases, creation of APIs, and data-driven maps and visualizations.

A NodeJS app has the basic following workflow.

*   _Import required modules_ − We use require directive to load a Node.js module.
*   _Create server_ − A server which will listen to client's request similar to Apache HTTP Server.
*   _Read request and return response_ − server created in earlier step will read HTTP request made by client which can be a browser or console and return the response. With these requests, various tasks can run. This can be page creation, content manipulation, database queries, and the creation of maps or visualizations.

To install Node, download it from the [Downloads page on the NodeJS homepage](https://nodejs.org/en/download/). Choose the operating system you are working on. You can install the most current version.

#### What do we mean by event-driven?

As soon as the NodeJS program is started, it is acting as a server. It initiates its variables, declares functions and then waits for event to occur. These events are triggered by the user or actions on the page.

Try a basic node tutorial to see how this works, if you are curious. Some of the following are great.

*   [nodeschool.io](http://nodeschool.io/)
*   [Felix's Node.js Beginners Guide](http://nodeguide.com/beginner.html)
*   [NodeJS on Google Cloud Tutorials](https://cloud.google.com/nodejs/)

### 3\. Install Express Framework

ExpressJS is a framework package that streamlines the NodeJS development process and provides a ‘server-like’ setup on the backend. When we say "framework", think along the lines of Wordpress, but for application development. It will create a folder template for us to use, set up our Node server and JavaScript files for us, and give us a location in which we can create content. We can then go in and customize as we see fit for our application.

The NodeJS ecosystem has many packages, or modules. Packages are software that makes usage of NodeJS easier or enables different functionality in a NodeJS ecosystem. For example, there are packages for managing plugins, installing libraries, and creating frameworks. NPM (Node Package Manager) is a package manager that makes it easy to install and work with packages when working with NodeJS.

#### a. Use Node Package Manager to Install

Using NPM, install Express and Express Generator. In Terminal, use NPM to install Express by typing the following

```
npm install express
npm install express-generator
```

If this doesn't work, you might need to use **sudo** and try again. Express will come with a handful of different items when it installs. One of these is Jade (or now, Pug). This is a templating engine that we will use to create our webpage. Borrowing from a fantastic post on [mapping with Node, Leaflet, and MongoDB by John McCrae](http://denelius.com/about/), here is some basic terminology:

*   app.js: the main NodeJS application driver
*   bin folder: internal workings
*   package.json – handles dependencies
*   public: this is for web content, and is the location for images, javascripts and stylesheets
*   routes: Kind of like a switchboard, routes handle and direct page requests
*   view: This is where HTML output is constructed. The default is to use the Jade(Pug) templating engine.

#### b. Create your project using Express

Make a directory for your project files. In terminal, change your working directory (**cd**) to this project folder. Here we will create our web application and set up all of our files. Once you are in your project folder, create a new project and give it a name. Since we are connecting to Postgres, let's call it **pg_mapper**. In terminal, in your project folder, run the following command to create your NodeJS project.

```
express pg_mapper
```

This will create your folder structure and required files based on the Express framework.

**cd** (change directory) into the project using:

```
cd pg_mapper
```

You are now in your project directory! In a Finder window, take a look at the folder structure for your project. You'll see the **app.js** file, along with the other files and folders that come with Express.

#### c. Add dependencies using package.json folder

Next, since NodeJS is JavaScript, we can set up a JSON that outlines our dependencies for the project. Dependencies will be items like the NodeJS Postgres Package, that allows us to connect to Postgres, or other packages. This is all setup in the **package.json** file. NodeJS will navigate the JSON and install our packages according to what it finds. This helps us keep our packages straight.

For this project, our package.json file should look like the following.

```
{
  "name": "pg_mapper",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.13.2",
    "cookie-parser": "~1.3.5",
    "debug": "~2.2.0",
    "express": "~4.13.1",
    "jade": "~1.11.0",
    "morgan": "~1.6.1",
    "serve-favicon": "~2.3.0",
    "pg": "*"
  }
}
```

The dependencies property contains all of the packages we want to install. Notable is the **pg** package, which installs Postgres.

_**Note: In 2016, Jade is being renamed to Pug, and the Jade language is currently being refactored. This will be updated when Pug is ready, for now, use Jade.**_

Save your package.json file in your project folder.

#### d. Install the Dependencies

To install the dependencies outlined in the **package.json** file, in your project folder, run the NPM install command. This will run through your JSON and install all of the packages included in the **dependencies** property.

```
npm install
```

### 4\. Test your Express App

To test your application, in your project folder (i.e. the base level that contains **app.js**), start your application using:

```
npm start
```

Navigate then to [http://localhost:3000](http://localhost:3000). You should see a basic 'Welcome to Express' page. If so, you are in business and we have a working application!

Stop your Node Server in terminal using **CTRL-C**.

Now, let's connect to our data and make a map.

### 5\. Setup our Page Router (index.js)

Navigate to the **routes** folder and find the **index.js** file. This is our server side JavaScript. Here we can set up a connection to the database, and using the Express router, create our HTTP requests, GET, PUT, etc. In this example, we are going to use GET requests, and filter data by passing a variable from the client to the server.

Our goal, in this file, is to perform the following.

*   Call required Node packages and libraries. _Should already be installed_
*   Set up the Database connection credentials string
*   Write a SQL query to get our data from PostGIS. _Query return should be in the form of GeoJSON_

Our **index.js** file should look like the following.

```
var express = require('express'); // require Express
var router = express.Router(); // setup usage of the Express router engine

/* PostgreSQL and PostGIS module and connection setup */
var pg = require("pg"); // require Postgres module
var conString = "postgres://username:password@localhost/databasename"; // Your Database Connection

// Set up your database query to display GeoJSON
var coffee_query = "SELECT row_to_json(fc) FROM ( SELECT 'FeatureCollection' As type, array_to_json(array_agg(f)) As features FROM (SELECT 'Feature' As type, ST_AsGeoJSON(lg.geom)::json As geometry, row_to_json((id, name)) As properties FROM cambridge_coffee_shops As lg) As f) As fc";

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

#### The Coffee Shop Query

The coffee shop query is important in this step. Here we query our database, and set up a JSON from the return. The query looks like the following:

<pre>SELECT row_to_json(fc) FROM (
	SELECT 'FeatureCollection' As type, array_to_json(array_agg(f)) As features FROM (
		SELECT 'Feature' As type, ST_AsGeoJSON(lg.geom)::json As geometry,
		row_to_json((id, name)) As properties FROM cambridge_coffee_shops As lg
	) As f
) As fc</pre>

Visit the [PostGIS documentation site](http://postgis.net/docs/manual-2.2/) for further details on the specific SQL language details.

Our database connection has been made. Next, we need to set up the routes to route to our individual pages.

### 6\. Test our API

To give you an idea of what is happening in **routes** and in **index.js**, let's setup a basic API grab. We can test our work so far by outputting a JSON element to the browser.

In the **index.js**, at the end of the document, set up a GET request using the Express router. This will display a JSON on our screen when we start our application and navigate to the site.

```
/* GET Postgres JSON data */
router.get('/data', function (req, res) {
    var client = new pg.Client(conString);
    client.connect();
    var query = client.query(coffee_query);
    query.on("row", function (row, result) {
        result.addRow(row);
    });
    query.on("end", function (result) {
        res.send(result.rows[0].row_to_json);
        res.end();
    });
});
```

Note that it requests a page named **data**, then in the request, connects to PostgreSQL, runs a query, adds the rows in the query return to a object named result, then sends the result to the page.

Save your **index.js** and run **npm start** to start your application back up.

```
npm start
```

Navigate in your browser to [http://localhost:3000/data](http://localhost:3000/data) and you should see the following.

It is a GeoJSON of our **cambridge_coffee_shops** table!

**Ctrl-C** to stop your server, now we can create the map of the this data.

### 7\. Set up our Map

Setting up our map involves creating the router request for our map page, connecting to our database, running the query on our database and returning a properly formatted JSON object, saving that object as a variable, and then rendering the map, passing the JSON object to the client to display.

In the **index.js** file, input the following. It creates a router request for our map.

```
/* GET the map page */
router.get('/map', function(req, res) {
    var client = new pg.Client(conString); // Setup our Postgres Client
    client.connect(); // connect to the client
    var query = client.query(coffee_query); // Run our Query
    query.on("row", function (row, result) {
        result.addRow(row);
    });
    // Pass the result to the map page
    query.on("end", function (result) {
        var data = result.rows[0].row_to_json // Save the JSON as variable data
        res.render('map', {
            title: "Express API", // Give a title to our page
            jsonData: data // Pass data to the View
        });
    });
});
```

Okay. Alot happened here, but it is actually very similar to how we created our JSON file that we displayed on our page. The difference is, on the end of the query we save our JSON as a variable named **data**, then we render our page, passing the **data** to our rendered page. We can refer to the **data** variable in our page at anytime by referencing:

```
!{jsonData} // our variable passed from index.js to our web page document
```

in the scripts in our page. This is powerful, as we can encode in the **index.js** the exact components we want to go to the client. Beyond that, all of our code in the **index.js** file stays on the server.

Save your **index.js** file. Now we need to create a page, or in Express terms, a 'View' for our map.

### 8\. Create our map View

Using Jade (and soon, PUG!), write up a simple map application. The code looks like the following.

```
extends layout
block content
    #map
    script.
        var myData = !{JSON.stringify(jsonData)};
        // Create variable to hold map element, give initial settings to map
        var map = L.map('map',{ center: [42.375562, -71.106870], zoom: 14});
        // Add OpenStreetMap tile layer to map element
        L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', { 
        	attribution: '© OpenStreetMap' 
        }).addTo(map);
        // Add JSON to map
        L.geoJson(myData,{
            onEachFeature: function (feature, layer) {
                layer.bindPopup(feature.properties.f2);
            }
        }).addTo(map);
```

Save this as **map.jade** in the **views** folder.

### 9\. Test our Map

In your project folder, we are ready to go. Run:

```
npm start
```

and we have started up our application!

Navigate in your browser to [http://localhost:3000/map](http://localhost:3000/map). You should see our map... and our dataset!

**CTRL-C** to stop your server.

### 10\. Add Data Filtering

Next, lets add some filtering to our map. Say, for example, you want to see only the 'Starbucks' locations of our coffee shop dataset. Let’s add a form input to our **map.jade** file. Set up the action property on our form to call a new Router request we can create in the **index.js**, called filter.

Our code will look like the following:

```
extends layout
block content
    form(action='/filter')
        select(name='name')
            option(disabled='', selected='', value='') Select One...
            option(value='Starbucks') Starbucks
            option(value='1369 Coffee House') 1369 Coffee House
        input(type='submit', value='Submit')
    #map
    script.
        var myData = !{JSON.stringify(jsonData)};
        // Create variable to hold map element, give initial settings to map
        var map = L.map('map',{ center: [42.375562, -71.106870], zoom: 14});
        // Add OpenStreetMap tile layer to map element
        L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', { attribution: '© OpenStreetMap' }).addTo(map);
        // Add JSON to map
        L.geoJson(myData,{
            onEachFeature: function (feature, layer) {
                layer.bindPopup(feature.properties.f2);
            }
        }).addTo(map);
```

#### Create the Filter router in the index.js

Next, we need to create a new route in the index.js that can hold a new query, passing the user input to the query. This is very similar to our other router requests. Because we set our form action to be **filter**, the new path should be called 'filter'.

#### Prevent a SQL Injection

We are prepping a query for database! We need to be careful though, because a web savvy visitor could send a statement to our database that modifies or deletes our data, so we want to make sure that users don't send malicious queries to our database. This is called a [SQL injection attack](http://www.w3resource.com/sql/sql-injection/sql-injection.php). One way we can do this is controlling our inputs by using a dropdown, but this is still not sufficient, so we should do a check on the query. Simple input check can prevent many attacks, so add the check in our **index.js** to make sure the query is friendly. See [this article](http://www.w3resource.com/sql/sql-injection/sql-injection.php) for a good overview and a summary of things to check for that might be malicious.

Here is our code:

```
/* GET the filtered page */
router.get('/filter*', function (req, res) {
    var name = req.query.name;
    if (name.indexOf("--") > -1 || name.indexOf("'") > -1 || name.indexOf(";") > -1 || name.indexOf("/*") > -1 || name.indexOf("xp_") > -1){
        console.log("Bad request detected");
        res.redirect('/map');
        return;
    } else {
        console.log("Request passed")
        var filter_query = "SELECT row_to_json(fc) FROM ( SELECT 'FeatureCollection' As type, array_to_json(array_agg(f)) As features FROM (SELECT 'Feature' As type, ST_AsGeoJSON(lg.geom)::json As geometry, row_to_json((id, name)) As properties FROM cambridge_coffee_shops As lg WHERE lg.name = \'" + name + "\') As f) As fc";
        var client = new pg.Client(conString);
        client.connect();
        var query = client.query(filter_query);
        query.on("row", function (row, result) {
            result.addRow(row);
        });
        query.on("end", function (result) {
            var data = result.rows[0].row_to_json
            res.render('map', {
                title: "Express API",
                jsonData: data
            });
        });
    };
});
```

Again, this is similar to the other requests, hoever this time, we want a new query to run, one that takes the input from the client.

Save your **index.js** file, and use **npm start** to start up your app again. You should see the following, with an dropdown form and submit at the top. Change the dropdown to **'Starbucks'** and hit submit. It will run the 'filter' request, returning your map with only the selected coffee shops!

Consider some next steps to expand this and check out our Leaflet map built with PostGIS/NodeJS/Express!

### Next Steps

This example is rudimentary, for sure, but you can see the value of using NodeJS and Express, along with PostGIS, in conjunction with Leaflet for web mapping. If you have a really big dataset, you only want to pass pieces of it to the client, not all of it. This can be a way that you can connect to big datasets and then filter.

A copy of all of the [source code for this project is on Github](https://github.com/mjfoster83/web-map-workshop "Workshop Materials") in the Web Map Workshop repository, along with a JSON of the dataset.

</div>
