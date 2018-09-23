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

_Last updated 23 September 2018_

## DRAFT

These are notes for a tutorial that introduces [Retention Rocket](https://www.retentionrocket.com/) to Rails developers.

## Introduction

> This "code column" contains code examples. Read the adjacent column for explanation and details.

In this tutorial, we learn how to track visits to an ecommerce website. Ecommerce tracking allows you to measure the number of transactions and revenue that your website generates. More importantly, ecommerce tracking helps you identify marketing campaigns that result in highest revenue.

You may be familiar with Google Analytics and Mixpanel, popular options for website tracking and analytics. Google Analytics primarily focuses on tracking page views to show usage of a website, though it can be configured to record user actions such as a visitor's progress through a sales funnel. Mixpanel focuses on tracking visitors rather than page views, which means once a visitor enters an email address or other identifying information, the visitor's page views and actions can be viewed in aggregate. Products such as Google Analytics and Mixpanel are designed to be feature-rich for non-programmers, with complex interfaces that require expertise to configure.

In this tutorial, we'll look at a "do-it-yourself" alternative for programmers that can simplify ecommerce tracking. We'll use the [Ahoy JavaScript analytics library](https://github.com/ankane/ahoy.js) in combination with a Rails application that uses the [Ahoy Ruby gem](https://github.com/ankane/ahoy). Rather than use a tool such as Google Analytics or Mixpanel, you can add the Ahoy JavaScript analytics script to any website and send tracking data to a Rails application. You can customize the integration as you wish and you won't pay for the service or rely on a third-party to store your data. You may also gather more data, since many users employ ad blockers that prevent Google Analytics from collecting data.

## Architecture

You can add the Ahoy JavaScript analytics script to any website (it doesn't have to be a Rails application). The Ahoy JavaScript library will send tracking data to any URL you set in the Ahoy configuration.

We will collect the tracking data by creating a Rails application that records the Ahoy API requests. We could create an API-only Rails application just to save the data to a database but in this case we'd like to visit the application and see reports.

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

## Rails Application with Ahoy
