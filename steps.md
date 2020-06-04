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