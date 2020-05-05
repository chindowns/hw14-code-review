# hw14-code-review# Code Review
An initial overview of the file structure and expectations for the application.
The application is a Nodejs server using server.js as the application server.  

## Required Node Packages
- express : Minimal and flexible server framework for Nodejs.
- express-session : Sessions are a library used to keep the user session authenticated when reloading pages using local stored cookies.  Asking the user to log in everytime a page reloads is a non-starter.
- bcryptjs : Password hashing function.  Enables the application to compare a usrs hashed password in the databse to the password coming from the client's browser.
- mysql : Provides database methods for the server to communicate with the database.
- sequelize : ORM library used to simplify common CRUD sequences to the database.
- passport : authentication library.
- passport-local : uses local email and password as authentiation.

## User Experience
When the user lands on the application URL, the browser displays the the signup.html if the browser does not have an active authentication token.  The user can go directly to the login page by clicking the link below the submit field.   

Once the user is logged in they are redirected to the members_html page.

## A Peek Under The Hood
After npm install, >server.js is run in a node session.  
### Server 
Server loads the required libraries
- express as 'express'
- express session as 'session'

- Next passport configuration is loaded (../config/passport.js).  Server references this at LOGIN.

#### Passport and Passport Configuration
-- Passport loads
--- the passport library as 'passport'
--- the passport-local.Strategy consturctor as 'localStategy'
--- the models (explained in Models)

-- 'passport' binds to localStrategy telling 'passport' to use email/password for authentication methods.
--- sets object key 'usernameField' to "email"
--- an anonymous function is loaded that uses sequelize to lookup the USER by their email.
---- .then waits for a result from the database lookup.
----- when the result returns without User info, a message is returned "incorrect email"

### Routes 
./routes/html-routes provides the routes for the HTML pages
./routes/api-routes provides routes to interface with the database.

db.sequelize.sync() initializes the database and creates the tables (models) if needed
    .then (function() {
        initializes the express node server to listen on PORT 8080
    });

## USER enters URL localhost:8080
If the user exists they are routed to the /members page.
If the user does not exist they are routed to signup.

### routes/html-routes.js
Path is required - Provides utilities for working with file and directory paths.

isAuthenticated creates an instance of Passports isAuthenticated class to check if the current user is authenticated.

Expert the 3 routes which SERVER.JS uses as noted above.
app.get("/" ... is the root route.  It has logic to determine if the requested user is currently authenticated or not. If they are authenticated the browser is redirected to "/members".  If they are not authenticated the browser is sent file "../public/"signup.html"

app.get("/login" is the route for logging in members.).
Once the member is authenticated, they are routed to /members.

app.get("/members", isAuthenticated... is the Passport middleware that checks if a user is Authenticated.) Once the user is authenticated the browser is sent the ../public/members.html to render.

### config/middleware/isAuthenticated.js
isAuthenticated is a function in the middleware Passport. It intercepts requests, checks if the request is authenticated.  If the request is not authenticated, the browser is rerouted.  In this case, it is rerouted to signup.

## USER is routed to signup
signup html loads displaying two input fields, the email, paswerd and confirm with a signup button.  The html also loads signup.js
#### Signup.js
There are event listeners on the signup button and listeners to read any data in the input fields.
on Submit validation checks that there is data in the email field and the password field.
When the validation passes, an api POST request is made to the api-routes to create the user.

## USER is routed to login
Much like signup, the user is provided an email and password input with a login button.  The html loads login.js
#### login.js
The difference here is that the api-routes.js route listening for login does a passport Authentication rather than a CREATE user.  If authenticated, the browser is rerouted to the members page.