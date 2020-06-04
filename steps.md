# Steps to go from ColdBox Zero to Hero

(All commands assume we are in the `box` shell unless stated otherwise.)

## 1 - Create the base app

### Create a folder for your app on your hard drive called `soapbox`

### Scaffold out a new Coldbox application with TestBox included

```sh
coldbox create app soapbox
```
`

### Start up a local server

```sh
start port=42518
```

### Open `http://localhost:42518/` in your browser. You should see the default ColdBox app template

### Open `/tests` in your browser. You should see the TestBox test browser

This is useful to find a specific test or group of tests to run _before_ running them.

### Open `/tests/runner.cfm` in your browser. You should see the TestBox test runner for our project

This is running all of our tests by default. We can create our own test runners as needed.

All your tests should be passing at this point. 😉

### Let's run the Tests via CommandBox

```sh
testbox run "http://localhost:42518/tests/runner.cfm"
```

### Lets add this url to our server.json

We can set the testbox runner into our server.json, and then we can easily run the tests at a later stage without having to type out the whole url. To do so, we use the `package set` command.

```sh
package set testbox.runner="http://localhost:42518/tests/runner.cfm"
testbox run
```

### Use CommandBox Test Watchers

CommandBox now supports Test Watchers. This allows you to automatically run your tests run as you make changes to tests or cfcs. You can start CommandBox Test watchers with the following command

```sh
testbox watch
```

You can also control what files to watch.

```sh
testbox watch **.cfc
```

`ctl-c` will escape and stop the watching.












## 2 - Intro to ColdBox MVC

### Development Settings

* Configure the `configure()` method for production
* Verify the `environment` structures
* Add the `development()` method settings

```js
function development(){
    coldbox.customErrorTemplate = "/coldbox/system/includes/BugReport.cfm";
    coldbox.handlersIndexAutoreload = true;
    coldbox.reinitPassword = "";
    coldbox.handlerCaching = false;
    coldbox.viewCaching = false;
    coldbox.eventCaching = false;
}
```

* Open the `layouts/Main.cfm` and add a tag for the environment

```html
<div class="badge badge-info">
    #getSetting( "environment" )#
</div>
```

* Where does the `getSetting()` method come from?
* Go back again to the UML Diagram of Major Classes (https://coldbox.ortusbooks.com/the-basics/models/super-type-usage-methods)
* Reinit the framework 

> http://localhost:42518?fwreinit=1

* What is cached?
* Singletons
* Handlers
* View/Event Caching

```bash
coldbox reinit
```

* Change the environments, test the label


### Show routes file. Explain routing by convention.

### Explain `event`, `rc`, and `prc`.

### Show `Main.index`.

### Explain views. Point out view conventions.

### Briefly touch on layouts.

### Show how `rc` and `prc` are used in the views.

### Add an about page

```bash
coldbox create view about/index
```

#### Add an `views/about/index.cfm`. Hit the url `/about/index` and it works!

```html
<cfoutput>
    <h1>About us!</h1>
</cfoutput>
```
#### Add an `about` handler, change to non-existent view, does it work?

```bash
coldbox create handler name="about" actions="index" views=false
```

#### Set it back to the `index` view, does it work?

#### Execute the tests, we have more tests now

* Explain Integration Testing, BDD Specs, Expectations
* Fix the tests

```js
it( "can render the about page", function(){
    //var event = execute( event="about.index", renderResults=true );
    //var event = execute( route="/about/index", renderResults=true );
    var event = GET( "/about" );

    expect(	event.getRenderedContent() ).toInclude( "About Us" );
});
```

### Assignment: Add a Links page

### Assignment: Change `prc.welcome` in the `handlers/main.cfc` and update the integration tests to pass.
















## 3 - Layouts

### Copy in simple bootstrap theme / layout to replace the existing `/layouts/main.cfm` layout.

```html
<cfoutput>
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <title>SoapBox</title>

        <base href="#event.getHTMLBaseURL()#" />

	    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
        <link rel="stylesheet" href="/includes/css/app.css">

        <script defer src="https://use.fontawesome.com/releases/v5.0.6/js/all.js"></script>
    </head>
    <body>
        <nav class="navbar navbar-expand-lg navbar-light bg-light fixed-top main-navbar">
            <a class="navbar-brand" href="#event.buildLink( url = "/" )#">
                <i class="fas fa-bullhorn mr-2"></i>
                SoapBox
            </a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="##navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
        </nav>

        <main role="main" class="container">
            #renderView()#
        </main>

        <footer class="border-top py-3 mt-5">
		<div class="container">
			<p class="float-right">
				<a href="##"><i class="fas fa-arrow-up"></i> Back to top</a>
			</p>

			<div class="badge badge-info">
				#getSetting( "environment" )#
			</div>

			<p>
				<a href="http://www.coldbox.org">ColdBox Platform</a> is a copyright-trademark software by
				<a href="http://www.ortussolutions.com">Ortus Solutions, Corp</a>
			</p>
			<p>
				Design thanks to
				<a href="http://getbootstrap.com/">Twitter Boostrap</a>
			</p>
		</div>
	</footer>

        <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
    </body>
</html>
</cfoutput>
```

### Insert the following CSS into a new file: `/includes/css/app.css`

```css
/* includes/css/app.css */

/* For fixed navbar */
body {
    font-family: "Lato", sans-serif;

    min-height: 75rem;
    padding-top: 4.5rem;
}

.main-navbar {
    box-shadow: 0 15px 30px 0 rgba(0, 0, 0, 0.11), 0 5px 15px 0 rgba(0, 0, 0, 0.08);
}
```























## 4 - Database and CBMigrations

### Install [commandbox-migrations](https://www.forgebox.io/view/commandbox-migrations)

```sh
install commandbox-migrations
```

You should see a list of available commands with `migrate ?`.

### Initalize migrations using `migrate init`

This adds the following to your `box.json` file:

```js
"cfmigrations":{
    "migrationsDirectory":"resources/database/migrations",
    "schema":"${DB_SCHEMA}",
    "connectionInfo":{
        "bundleName":"${DB_BUNDLENAME}",
        "bundleVersion":"${DB_BUNDLEVERSION}",
        "password":"${DB_PASSWORD}",
        "connectionString":"${DB_CONNECTIONSTRING}",
        "class":"${DB_CLASS}",
        "username":"${DB_USER}"
    },
    "defaultGrammar":"AutoDiscover@qb"
}
```

Which is needed so migrations can talk to your database!

### Install [commandbox-dotenv](https://www.forgebox.io/view/commandbox-dotenv)

To make our migration setup more secure, we're going to use environment variables. In local development, we can do this easily with a commandbox module, DotEnv.

```sh
install commandbox-dotenv
```

### Update the existing `/.env` file. Fill it in appropraitely. (We'll fill it in with our Docker credentials from before.)

This assumes you are using MySQL with CommandBox 5 or greater, which the newer version of Lucee requires a BundleName and BundleVersion and a new class for the driver.

```sh
DB_CONNECTIONSTRING=jdbc:mysql://127.0.0.1:3306/soapbox?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC&useLegacyDatetimeCode=true
DB_CLASS=com.mysql.cj.jdbc.Driver
DB_BUNDLENAME=com.mysql.cj
DB_BUNDLEVERSION=8.0.15
DB_DRIVER=MySQL
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=soapbox
DB_SCHEMA=soapbox
DB_USER=root
DB_PASSWORD=soapbox
```

Once the `.env` file is seeded, reload CommandBox so it can pickup the environment: `reload`

### Test our environment variable with an echo command

```sh
echo ${DB_BUNDLEVERSION}
```

You should see no output, because DotEnv has not loaded the variables from our file.

### Reload your shell in your project root. (`reload` or `r`)

```sh
r
echo ${DB_BUNDLEVERSION}
```

You should now see `8.0.15`, your `DB_BUNDLEVERSION` output when you run that `echo` command.

### Install cfmigrations using `migrate install`. (This will also test that you can connect to your database.)

```sh
migrate install
```

If the table does not exist, this will create the table in your db. If you refresh your db, you should see the table. If you run the command again, it will let you know it is already installed. Try it!

### Create a users migration

```sh
migrate create create_users_table
```

All migration resources are CFCs and are stored under `resources/database/migrations/**.cfc`.  Make sure these are in version control. They can save your life!

### Fill in the migration.

The migration file was created by the last command, and the file location was output by commandbox.
If you are using VS Code, you can just `Ctrl` + Click to open the file.

```java
component {

    function up( schema ) {
        queryExecute( "
            CREATE TABLE `users` (
                `id` INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
                `username` VARCHAR(255) NOT NULL UNIQUE,
                `email` VARCHAR(255) NOT NULL UNIQUE,
                `password` VARCHAR(255) NOT NULL,
                `createdDate` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                `modifiedDate` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
            )
        " );

        // schema.create( "users", function( table ) {
        //     table.increments( "id" );
        //     table.string( "username" ).unique();
        //     table.string( "email" ).unique();
        //     table.string( "password" );
        //     table.timestamp( "createdDate" );
        //     table.timestamp( "modifiedDate" );
        // } );
    }

    function down( schema ) {
        queryExecute( "DROP TABLE `users`" );

        // schema.drop( "users" );
    }

}
```

* Go over file and describe the options you can have to create the schemas.
* QB Schema Builder Docs: https://qb.ortusbooks.com/schema-builder/schema-builder

### Run the migration up.

```sh
migrate up
```

Check your database, and you should see the database table. You can migrate `up` and `down` to test both functions. Go for it, tear it down: `migrate down`, and now back up: `migrate up`.

> If all else fails: `migrate fresh` is your best bet! (https://www.forgebox.io/view/commandbox-migrations)

### Next add the following settings into your `/Application.cfc` file

This will add the datasource to your CFML engine.  There are other ways, but this is the easiest and portable.

```js
this.datasource = "soapbox";
```

### Refresh your app, and you will see an error.

`Could not find a Java System property or Env setting with key [DB_CLASS].`

This is because although we reloaded the CLI so migrations is able to read those values... we did not restart our server, so Java does not have those variables.

`server restart`

Now when you restart, Java is passed all of those variables automatically by the DotEnv module, and now this will work.
You will need to restart your server whenever you add, remove, or change environment variables.

### Ensure your app and your tests are running

Hit `/` in your browser
Hit `/tests/runner.cfm` in your browser


























## 5 - Setup the Test Harness and Base Spec

### Install `cfmigrations` as a dev dependency. 

CF Migrations is different than `commandbox-migrations`. It allows you to run the migrations from a running CFML engine and NOT CommandBox.  Usually, you can use them for testing purposes or delivering updates in your apps.  However, for today, this is a development dependency only.

```sh
install cfmigrations --saveDev
```

### Configure `tests/Application.cfc`

```js
// tests/Application.cfc
this.datasource = "soapbox";
```

### Create a `tests/resources/BaseIntegrationSpec.cfc`

```js
component extends="coldbox.system.testing.BaseTestCase" {

    property name="migrationService" inject="MigrationService@cfmigrations";

    this.loadColdBox    = true;
    this.unloadColdBox  = false;

    /**
     * Run Before all tests
     */
    function beforeAll() {
        super.beforeAll();
        // Wire up this object
        application.wirebox.autowire( this );

        // Check if migrations ran before all tests
        if ( ! request.keyExists( "migrationsRan" ) ) {
            migrationService.setMigrationsDirectory( "/root/resources/database/migrations" );
            migrationService.setDatasource( "soapbox" );
            migrationService.runAllMigrations( "down" );
            migrationService.runAllMigrations( "up" );
            request.migrationsRan = true;
        }
    }

    /**
     * This function is tagged as an around each handler.  All the integration tests we build
     * will be automatically rolledbacked
     * 
     * @aroundEach
     */
    function wrapInTransaction( spec ) {
        transaction action="begin" {
            try {
                arguments.spec.body();
            } catch ( any e ){
                rethrow;
            } finally {
                transaction action="rollback";
            }
        }
    }

}
```

Let's run the tests again!




















## 6 - Intro to Models - User Service

Models are at the core of ColdBox. We could teach you how to write legacy style code, but we want to teach the `right` way from the start. We still start with creating a `User Service` that will be managing users in our SoapBox.

```bash
coldbox create model name="UserService" persistence="singleton"
```

This  will create the `UserService.cfc` in the `models` folder and a companion test in `tests/specs/unit/UserServiceTest.cfc`.

### BDD - Let's write our tests first

Open up the test `/tests/specs/unit/UserServiceTest.cfc`

```js
function run() {
    describe( "User Service", function() {
        it( "can list all users", function() {
            fail( "test not implemented yet" );
        } );
    } );
}
```

Hit the url: http://127.0.0.1:42518/tests/runner.cfm to run your tests. The test will run and fail as expected. As we use BDD we will write the real test.

### Let's write the real test - step 1

```js
    function run() {
        
        describe( "User Service", function() {
			
			it( "can be created", function(){
				expect( model ).toBeComponent();
			});

            it( "can list all users", function() {
			} );
			
        } );

    }
```

Run your tests `/tests/runner.cfm` 

### Lets create the `list()` function

Lets test the `list()` function exists

Update the `/tests/specs/integration/UserServiceTest.cfc`, adding the content to the following `it` block.

```js
it( "can list all users", function() {
    var aResults = model.list();
} );
```

Run the tests and you'll get the following error `component [models.UserService] has no function with name [list]`

Lets create the `list()` function

```js
function list(){
    return "";
}
```

Run the tests and you'll see you tests pass.

### Lets make the `list()` function return the data

Update the `/tests/specs/integration/UserServiceTest.cfc`, adding the content to the following `it` block. This will check the return type, to ensure the return is an array.

```js
it( "can list all users", function() {
    var aResults = model.list();
	expect( aResults ).toBeArray();
} );
```

Run the tests, and they will fail with the following error. `Actual data [] is not of this type: [Array]`

```js
function list(){
    return queryExecute( "select * from users", {}, { returntype = "array" } );
}
```

Run the tests, and the test should pass.







## 7 - Using Models in Handlers and Views

### Inject the `UserService` into your Main Handler.

Add the injection into your `/handlers/Main.cfc` 

```js
property name="userService"		inject="UserService";
```

### Use the `userService.list()` call to retrieve the user list

Call the userService.list() function and store it in a prc variable.

```js
function index(event,rc,prc){
    prc.userList = userService.list();
    prc.welcomeMessage = "Welcome to ColdBox!";
    event.setView("main/index");
}
```

Hit `/` in your browser, and you'll get an error - `Messages: variable [USERSERVICE] doesn't exist.`  What happened? Who can tell me why are we getting this error?


### Reinit the Framework

Hit '/?fwreinit=1' and now you will not see the error.

### Check the List call is working

Add a writeDump in the handler, to ensure the call is succeeding.

```js
writeDump( prc.userList );abort;
```

You'll see something like this

```sh
Array (from Query)
Template: /YourAppDirectory/models/UserService.cfc 
Execution Time: 1.08 ms 
Record Count: 0 
Cached: No 
SQL: 
select * from users 
```

### Access the data from the View

Remove the `writeDump` from the handler.

Add the following into the `/views/main/index.cfm` file, replacing the contents completely.

```html
<cfdump var="#prc.userList#">
```

Reload your `/` page, and you'll see the layout ( which wasn't present in the handler dump ) and the dump of an empty array.

Now we can build our registration flow.

























## 8 - Building the Registration Flow

Let's begin by creating the registration flow by generating our `registration` handler:

```bash
coldbox create handler name="registration" actions="new,create"
```

The `create` action does not have a view, so let's clean that up: `delete views/registration/create.cfm`

### BDD

Open `tests/specs/integration/RegistrationTest.cfc` and modify

```js
component extends="tests.resources.BaseIntegrationSpec"{
	
	property name="query" inject="provider:QueryBuilder@qb";

	/*********************************** BDD SUITES ***********************************/
	
	function run(){

		describe( "Registration Suite", function(){

			beforeEach(function( currentSpec ){
				// Setup as a new ColdBox request for this suite, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			});

			it( "can show the user registration form", function(){
				var event = get( route="registration.new", params={} );
				// expectations go here.
				expect( false ).toBeTrue();
            });
            
            it( "can register a user", function(){
				var event = post( route="registration.create", params={} );
				// expectations go here.
				expect( false ).toBeTrue();
			});

		
		});

	}

}
```

Hit the url: http://127.0.0.1:42518/tests/runner.cfm to run your tests. The test will run and fail as expected. As we use BDD we will write the real specs:

```js

component extends="tests.resources.BaseIntegrationSpec"{

	/*********************************** BDD SUITES ***********************************/

	function run(){

		feature( "User Registrations", function(){

			beforeEach(function( currentSpec ){
				// Setup as a new ColdBox request for this suite, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			});

			it( "should present a new registration screen", function(){
				var event = GET( "registration/new" );
				// expectations go here.
				expect( event.getRenderedContent() ).toInclude( "Register for SoapBox" );
			});

			story( "I want to register users", function(){

				given( "valid data", function(){
					then( "I should register a new user", function() {
						expect(
							queryExecute(
								"select * from users where username = :username",
								{ username : "testadmin" },
								{ returntype = "array" }
							)
						).toBeEmpty();

						var event = POST( "/registration", {
							username : "testadmin",
							email : "testadmin@test.com",
							password : "password",
							passwordConfirmation : "password"
						} );

						// expectations go here.
						expect( event.getValue( "relocate_URI" ) ).toBe( "/" );

						expect(
							queryExecute(
								"select * from users where username = :username",
								{ username : "testadmin" },
								{ returntype = "array" }
							)
						).notToBeEmpty();
					})
				});

				xgiven( "invalid registration data", function(){
					then( "I should show a validation error", function() {
						fail( "implement" );
					})
				});

				xgiven( "a non-unique email", function(){
					then( "I should show a validation error", function() {
						fail( "implement" );
					})
				});

				xgiven( "a non-unique username", function(){
					then( "I should show a validation error", function() {
						fail( "implement" );
					})
				});

			} );


		});

	}

}
```

Now let's move to implementation.

### Resourceful Routes

**SHOW RESOURCES ROUTING TABLE. EXPLAIN WHY RESOURCES.**
https://coldbox.ortusbooks.com/the-basics/routing/routing-dsl/resourceful-routes

Update the `/config/Router.cfc` file - insert a resources definition.

```js
// config/Router.cfc
function configure(){
    setFullRewrites( true );
    
    resources( "registration" );

    route( ":handler/:action?" ).end();
}
```

When working with routes it is essential to visualize them as they can become very complex.  We have just the module for that. Go to your shell and install our awesome route visualizer: `install route-visualizer --saveDev`.  Now issue a reinit: `coldbox reinit` and refresh your browser.  You can navigate to: http://localhost:42518/route-visualizer and see all your wonderful routes.

### Event Handler - new() action

Revise the actions in the Registration Handler `handlers/registration.cfc`

```js
function new( event, rc, prc ) {
    event.setView( "registration/new" );
}

function create( event, rc, prc ) {
    event.setView( "registration/create" ); // REMOVE THIS
}
```


### Update the `new` view

Add the following into a new file `views/registration/new.cfm`

```html
<!-- views/registration/new.cfm -->
<cfoutput>
    <div class="card">
        <h4 class="card-header">Register for SoapBox</h4>
        <form class="form panel card-body" method="POST" action="#event.buildLink( "registration" )#">
            <div class="form-group">
                <label for="email" class="control-label">Email</label>
                <input id="email" name="email" type="email" class="form-control" placeholder="Email" />
            </div>
            <div class="form-group">
                <label for="username" class="control-label">Username</label>
                <input id="username" name="username" type="text" class="form-control" placeholder="Username" />
            </div>
            <div class="form-group">
                <label for="password" class="control-label">Password</label>
                <input id="password" name="password" type="password" class="form-control" placeholder="Password" />
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-primary">Register</button>
            </div>
        </form>
    </div>
</cfoutput>
```

Hit http://127.0.0.1:42518/registration/new

Now you will see the form.

### NavBar Updates

Add a register link to the navbar for our registration page

```html
<div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav ml-auto">
        <a href="#event.buildLink( "registration.new" )#" class="nav-link">Register</a>
    </ul>
</div>
```

The Nav bar is located in our Layout file `/layouts/Main.cfm`. Insert the code above, into the `<nav>` tag, before the closing `</nav>` .

Refresh your page, click Register and fill out the form. Submit the form and you will see an error
`Messages: The event: Registration.create is not a valid registered event.`

Next we'll create the saving action. Which is what our test was written for.

### Event Handler - create() action

```js
function create( event, rc, prc ) {
    //insert the user

    relocate( uri = "/" );
}
```

We need to inject the `UserService` into the handler. Add the following code to the top of the Registration.cfc handler.

```js
//handlers/Registration.cfc
property name="userService"		inject="UserService";
```

Remove `//insert the user` and replace with

```js
var generatedKey = userService.create( rc.email, rc.username, rc.password );
flash.put( "notice", {
    type : "success",
    message : "The user #encodeForHTML( rc.username )# with id: #generatedKey# was created!"
} );
```
#### Update Layout With Flash

What is new to you here? Flash scope baby! Let's open the `layouts/Main.cfm` and create the visualization of our flash messages:

```html
<cfif flash.exists( "notice" )>
    <div class="alert alert-#flash.get( "notice" ).type#">
    #flash.get( "notice" ).message#
    </div>
</cfif>
```

### Model Updates

Do not ever use a password that is un-encrypted, it is not secure, so lets use `BCrypt`. On ForgeBox, there is a module for that and easily installable with CommandBox.

#### Add [BCyrpt](https://github.com/coldbox-modules/cbox-bcrypt)

```sh
install bcrypt
```

Update the `UserService` to use BCrypt `/models/UserService.cfc`
We are adding the DI Injection for the BCrypt Module.

```js
component singleton accessors="true"{
	
	// Properties
	property name="bcrypt" inject="@BCrypt";
```

#### Let's create the `create` method in the UserService

Create the `create` function, that has 3 arguments, and write the query, including wrapping the password in a call to bcrypt to encrypt the password.

```js
/**
 * Create a new user
 *
 * @email 
 * @username 
 * @password 
 * 
 * @return The created id of the user
 */
numeric function create(
    required string email,
    required string username, 
    required string password
){
    queryExecute( 
        "
            INSERT INTO `users` ( `email`, `username`, `password` )
            VALUES ( ?, ?, ? )
        ",
        [ 
            arguments.email, 
            arguments.username, 
            bcrypt.hashPassword( arguments.password )
        ],
        {
            result : 'local.result'
        }
    );

    return local.result;
}
```

#### Verify Registration

Hit the url: http://127.0.0.1:42518//registration/new and add a new user.

If you didn't reinit the framework, you will see the following error `Messages: variable [BCRYPT] doesn't exist` Dependency Injection changes require a framework init.

Now hit the url with frame reinit: http://127.0.0.1:42518//registration/new?fwreinit=1

Add a new user, and see that the password is now encrypted. Bcrypt encrypted passwords look like the following:

`$2a$12$/w/nkNrV6W6qqZBNXdqb4OciGWNNS7PCv1psej5WTDiCs904Psa8S`

Check your tests, they should all pass again.

### Complete the steps and Register yourself

**SELF DIRECTED** (20 minutes)

What happens if you run the tests again? Where is your user?