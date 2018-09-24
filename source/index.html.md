---
title: Ecommerce Tracking with Ahoy

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:
  - This document is open source
  - Questions? <a href='https://stackoverflow.com/questions/tagged/ruby-on-rails'>Ask on Stack Overflow</a>
  - Have fixes? <a href='https://github.com/RailsApps/tutorial-ecommerce-tracking'>Edit the GitHub repo</a>


includes:

search: true
---

# Ecommerce Tracking with Ahoy

**by Daniel Kehoe**

_Last updated 24 September 2018_

## DRAFT

These are notes for a tutorial that introduces [Retention Rocket](https://www.retentionrocket.com/) to Rails developers.

## Introduction

> This "code column" contains code examples. Read the adjacent column for explanation and details.

In this tutorial, we learn how to track visits to an ecommerce website. Ecommerce tracking allows you to measure the sales and marketing activity on your website. For example, ecommerce tracking helps you identify marketing campaigns that result in highest revenue.

You may have heard of Google Analytics and Mixpanel, popular options for website tracking and analytics. Google Analytics primarily focuses on tracking page views to show usage of a website, though it can be configured to record user actions such as a visitor's progress through a sales funnel. Mixpanel focuses on tracking visitors, which means once a visitor enters an email address or other identifying information, the visitor's page views and actions can be viewed in aggregate. Products such as Google Analytics and Mixpanel are designed to be feature-rich for non-programmers, with complex interfaces that require expertise to configure.

In this tutorial, we'll look at a "do-it-yourself" alternative for programmers that can simplify ecommerce tracking. For a technically adept programmer, building your own ecommerce tracking system actually may take less effort than learning how to use Google Analytics and Mixpanel (and you'll have an in-depth understanding of your tools).

We'll use the [Ahoy JavaScript analytics library](https://github.com/ankane/ahoy.js) in combination with a Rails application that uses the [Ahoy Ruby gem](https://github.com/ankane/ahoy). You can add the Ahoy JavaScript analytics script to any website and send tracking data to a Rails application. You can customize the integration as you wish and you won't pay for the service or rely on a third-party to store your data. You may also gather more data, since many users employ ad blockers that prevent Google Analytics from collecting data.

## Architecture

You can add the Ahoy JavaScript analytics script to any website (it doesn't have to be a Rails application). The Ahoy JavaScript library will send tracking data to any URL you set in the Ahoy configuration.

We will collect the tracking data by creating a Rails application that records the Ahoy API requests. We could create an API-only Rails application just to save the data to a database but in this case we'd like to visit the application and see reports.

A Google search for "Ahoy analytics example" will reveal several tutorials that show how to add the Ahoy Ruby gem to a Rails application so you can track activity within the application. These monolithic applications combine all functions within a single application and are suitable for simple applications which will not grow in complexity. In contrast, this tutorial shows you how to add the Ahoy JavaScript analytics script to any website and capture tracking data with a separate Rails application. Gathering and sending visitor data is a separate function from recording visitor data and displaying reports. If you expect your application to grow in complexity, separating the "gathering" function from the "storing and reporting" function may be worthwhile. This service-oriented architecture (SOA) is the approach we describe in this tutorial.

## Scope and Tasks

The primary purpose of this tutorial is to introduce the concepts you'll need to understand the implementation of ecommerce tracking. To that end, we will build a simple generic website with HTML and CSS and add the Ahoy JavaScript analytics library.

To record the tracking data, we'll build a Rails application and add the [Ahoy Ruby gem](https://github.com/ankane/ahoy) that records Ahoy API event requests.

After we've confirmed that the Rails application captures data from the simple generic website, we'll build an ecommerce website in Rails and implement event tracking using the Ahoy API. With the experience you gain, you'll be able to integrate the Ahoy tracking library with any ecommerce website you build and capture the data with a Rails application.

You'll also have a solid understanding for integrating sophisticated ecommerce strategies such as retention marketing with the [Retention Rocket](https://www.retentionrocket.com/) platform. Retention Rocket uses a JavaScript library that is similar to the Ahoy library and sends event data to track and respond to events such as cart abandonment. Our tutorial [Retention Marketing with Retention Rocket](https://railsapps.github.io/tutorial-retention-marketing/) goes in depth to show how to reduce cart abandonment and increase site revenue.

## Simple Website

We'll start by building a simple website. We'll add the Ahoy JavaScript analytics library so we can visit the website home page and trigger an API request that sends event data to a remote server. To save effort and get a web page that looks realistic, we'll download a Bootstrap template from the [Start Bootstrap](https://startbootstrap.com/) site.

Visit the [Shop Homepage](https://startbootstrap.com/template-overviews/shop-homepage/) and click the download button to get a zip archive file on your local machine. Open the zip archive and rename the folder to "GenericStore."

### Add the Ahoy JavaScript Library

```html
<head>

  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>Shop Homepage - Start Bootstrap Template</title>

  <!-- Bootstrap core CSS -->
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">

  <!-- Custom styles for this template -->
  <link href="css/shop-homepage.css" rel="stylesheet">

  <!-- Ahoy analytics -->
  <script src="https://unpkg.com/ahoy.js@0.3.3/dist/ahoy.js"></script>

</head>
```

Inside the folder is an *index.html* file. Open the file in a text editor and edit the `<head>` section. Add a line before the final `</head>` tag:

`<script src="https://unpkg.com/ahoy.js@0.3.3/dist/ahoy.js"></script>`

This directive will download and include the Ahoy JavaScript analytics library from the Unpkg content delivery network. Alternatively, you can download and save the JavaScript file to the GenericStore folder and add an appropriate `<script>` tag. Visit the GitHub repository for the [Ahoy JavaScript analytics library](https://github.com/ankane/ahoy.js) to learn more about the library and its functions.

<aside class="notice">
The Developer Tools view will be your primary diagnostic tool for front-end development. In Chrome on macOS, press Command-Option-I to open the Developer Tools View in a section of the browser window. Alternatively, you can find the menu item under View/Developer/Developer Tools. In Chrome on Windows or Linux platforms, press Shift-Ctrl-I or select Menu/Tools/Developer Tools.
</aside>

View the web page by opening the *index.html* file in a web browser. Use Google Chrome Developer Tools to verify that the *ahoy.js* file is loaded (it will appear under the Sources and Network tabs).

### Test the Ahoy JavaScript Library

```console
> ahoy.trackAll();
ahoy.js:317 Uncaught TypeError: Failed to execute 'sendBeacon'
on 'Navigator': Beacons are only supported over HTTP(S).
```

Switch to the Console tab in Developer Tools to test the Ahoy JavaScript analytics library. Enter a Javascript directive to test if the Ahoy library responds.

`ahoy.trackAll();`

The console will display an error. You've opened an HTML file from the local file system in the web browser. Ahoy will only work if the web browser requests the page from a web server. You can run a local web server to test Ahoy.

To view the Generic Store home page from a local web server, you must have Node.js, npm, and Gulp installed globally on your machine. You can [install Node.js](https://nodejs.org/en/) to get Node.js and npm. After npm is installed, install gulp with `npm install gulp`. [Node.js](https://nodejs.org/en/) provides a local web server. [NPM](https://www.npmjs.com/) is a package manager for JavaScript libraries that run under Node. [Gulp](https://gulpjs.com/) is a build tool for JavaScript development. There are other ways to deploy an html file with a local web server, but the Start Bootstrap template is set up conveniently for Node.js, npm, and Gulp.

```console
$ node -v
v8.5.0
$ npm -v
6.4.1
$ gulp -v
[...] Local version 3.9.1
```

Check that Node.js, npm, and Gulp are installed.

`$ node -v`

`$ npm -v`

`$ gulp -v`

```console
$ gulp dev
[...] Using gulpfile ~/workspace/rr/GenericStore/gulpfile.js
```

Next you will start the local web server using Gulp. The Generic Store home page will be available in your web browser at [http://localhost:3000](http://localhost:3000).

`$ gulp dev`

```console
> ahoy.trackAll();
undefined
POST http://localhost:3000/ahoy/events 404 (Not Found)
```
Open the Chrome Developer Tools console and enter:

`ahoy.trackAll();`

The Ahoy library will attempt to send a `POST` request to the local web server and you'll see a `404` error message that the local web server cannot find the URL where Ahoy is sending the request. This is progress! Earlier, Ahoy was unable to send tracking data. Now Ahoy is sending tracking data but it is not configured to send the tracking data to a server that can process the API requests.

At this point, we have a simple web page with the Ahoy JavaScript analytics library integrated for sending tracking data. We can view the simple web page in a local web server (running under Node.js) and tracking data will be sent to another application using Ahoy API requests. However, we don't have another application running to receive the Ahoy API requests. Next we will build a Rails application to collect the tracking data sent by Ahoy.

## New Application with Rails Composer

```console
$ ruby -v
ruby 2.5.1 ...
$ rails -v
Rails 5.2.0
$ rails new ecommerce-tracking -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb
.
.
.
option  Build a starter application?
    1)  Build a RailsApps example application
    2)  Contributed applications
    3)  Custom application (experimental)
choose  Enter your selection: 2
option  Choose a starter application.
    1)  rails-shortcut-app
choose  Enter your selection: 1
.
.
.
$ cd ecommerce-tracking
$ bundle install
...
$ rails -T
...
```

If you're new to Rails, you can learn [how to install Rails](http://railsapps.github.io/installrubyonrails-mac.html) and set up your development environment. Let's check that Ruby and Rails are installed. You'll need Ruby 2.5 (or newer) and Rails 5.2 (or newer) on your computer to follow this tutorial.

`$ ruby -v`

`$ rails -v`

We'll use [Rails Composer](http://www.railscomposer.com/) to build the starter application named "ecommerce-tracking". Rails Composer will give us a fully functional application, complete with Devise for authentication, role-based authorization, and the Bootstrap frontend framework. Here's how to generate a starter application with Rails Composer:

`$ rails new ecommerce-tracking -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb`

We'll name our application "ecommerce-tracking" but you can use any name you like. Choose:

`2)  Contributed applications`

and

`1)  rails-shortcut-app`

When Rails Composer finishes generating the application, change directories and run `bundle install`.

`$ cd ecommerce-tracking`

`$ bundle install`

You can confirm the application is functional by running a "smoke test" with `rails -T`. You should see a list of Rails commands.

You can run the application server to see if the application works.

```console
$ rails server
=> Booting Puma
=> Rails 5.2.0 application starting in development
.
.
.
```

`$ rails server`

View the app running at [http://localhost:3000/](http://localhost:3000/). You'll see a welcome page with a navigation bar that includes a "Sign in" link. You can sign in with the email address `user@example.com` and the password `changeme` (the credentials are set in the *config/secrets.yml* file).

Rails Composer has generated a full functional Rails application. The rails-shortcut-app you've built is very similar to the [rails-devise-roles](https://github.com/RailsApps/rails-devise-roles) example application. See the [rails-devise-roles](https://github.com/RailsApps/rails-devise-roles) documentation if you'd like to learn how Devise authentication or role-based authorization is set up.

Next we'll add Ahoy to the application so we can track visits and events.

## Add Ahoy to the Application

```ruby
# Gemfile
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }
ruby '2.5.1'
...
# gems for ecommerce tracking
gem 'ahoy_matey'
```

Open the Gemfile and add the Ahoy Ruby gem. Add the gem at the end of the file:

`gem 'ahoy_matey'`

```console
$ bundle install
...
$ rails generate ahoy:install
create  config/initializers/ahoy.rb
create  app/models/ahoy/visit.rb
create  app/models/ahoy/event.rb
create  db/migrate/20180923075710_create_ahoy_visits_and_events.rb

Almost set! Last, run:

rails db:migrate
$ rails db:migrate
...
```

Install the gem with Bundler and run the Ahoy install script and a database migration:

`$ bundle install`

`$ rails generate ahoy:install`

`$ rails db:migrate`

The Ahoy Rails generator creates a configuration file *config/initializers/ahoy.rb* and two Ahoy models for a `Visit` and `Event`. The database migration adds two new tables (for visits and events). You can see the new tables in the *db/schema.rb* file.

Commit your work to git with a message "add Ahoy".

### Modify the Application Controller

```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  after_action :track_action

  protected

  def track_action
    ahoy.track "#{request.method} #{request.fullpath}", request.filtered_parameters.to_s
  end
end
```
Modify the application controller to record an Ahoy event to the database for every page request.

See the code sidebar. You'll add an `after_action :track_action` to the controller and a `track_action` method. The `track_action` method calls the `ahoy.track` method provided by the Ahoy Ruby gem, writing some request parameters to the database. We've added the `ahoy.track` method to the application controller so that every request to the application is recorded.

### Test the Rails application

Start the Rails server:

`$ rails server`

Visit the application home page at [http://localhost:3000/](http://localhost:3000/). You'll see the application home page.

If you look at the console, you will see Ahoy records a `Visit` in the database:

`Ahoy::Visit Create (...)  INSERT INTO "ahoy_visits" ...`

The visit is automatically created by Ahoy and records information about the landing page and the browser, plus tokens that track the visit (the session) and the visitor.

Ahoy also records an `Event` in the database because we've added the `ahoy.track` method to the application controller:

`Ahoy::Event Create (...)  INSERT INTO "ahoy_events" ...`

The event is automatically associated with the visit. That means we can create a database query that shows all events initiated by the visitor during the session.  We've created a custom event that records the controller name and action.

We can use the Rails console to observe the records in the database. Quit the Rails server (or open a new terminal session), start the Rails console, and query the database:

`$ rails console`

`$ irb(main):001:0> Ahoy::Visit.last`

`$ irb(main):002:0> Ahoy::Event.last`

You'll see the database records for the `Visit` and `Event`.

At this point we've got a Rails application that records visits and custom events. We can see the database records in the Rails console but we don't have an easy way to display records or generate reports. There's another problem: We are recording visits to our dedicated Rails reporting application. We don't want to record visits to our administrative application. We want to capture API requests originating from our simple Generic Store web page.

Next we'll reconfigure Ahoy to ignore visits to the application and record API requests instead.

### Configure for API Requests

```ruby
# config/initializers/ahoy.rb
class Ahoy::Store < Ahoy::DatabaseStore
end

# set to true for JavaScript tracking
Ahoy.api = true
Ahoy.server_side_visits = false

# better user agent parsing
Ahoy.user_agent_parser = :device_detector
```

We'll remove our changes to the application controller so we are no longer recording server-side events. Just delete the changes and save the file *app/controllers/application_controller.rb* or run `git checkout app/controllers/application_controller.rb` to revert the changes.

The Ahoy Ruby gem can be configured to respond to API requests and ignore server-side visits. Modify the file *config/initializers/ahoy.rb*:

`Ahoy.api = true`

`Ahoy.server_side_visits = false`

Restart the Rails server and visit the application home page. Check the console log and make sure that visits are not getting recorded. You should see ordinary Rails page requests but no writes of Ahoy visits or events.

Commit your work to git with a message "configure Ahoy for API requests".

You can leave the Rails application running. Next we'll test to see if the Rails application will capture API requests from the Generic Store web page.

## Test API Requests

Let's see if we can generate API requests from the Generic Store web page and record the visits and events in our Rails application.

### Configure Ahoy

```html
<script>
  console.log('loading ahoy.configure');
  ahoy.configure({
    urlPrefix: "http://localhost:3000",
    visitsUrl: "/ahoy/visits",
    eventsUrl: "/ahoy/events",
    cookieDomain: null,
    page: null,
    platform: "Web",
    useBeacon: false,
    startOnReady: true,
    trackVisits: true
  });
</script>
```

We'll add JavaScript to configure `ahoy.js` to send events to [http://localhost:3000/](http://localhost:3000/).

### Launch the Generic Store Server

```console
$ gulp dev
[...] Using gulpfile ~/workspace/rr/GenericStore/gulpfile.js
```

You can leave the Rails application running with its server responding to [http://localhost:3000/](http://localhost:3000/).

Launch the Node.js server to serve the Generic Store web page.

`$ gulp dev`

With the Rails application running on port 3000, the Node.js server recognizes the port is active and launches on port 3001.

### Issue: No 'Access-Control-Allow-Origin'

In the JavaScript console, you'll see an error:

`Failed to load http://localhost:3000/ahoy/events: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:3001' is therefore not allowed access.`

Next we'll resolve this issue.
