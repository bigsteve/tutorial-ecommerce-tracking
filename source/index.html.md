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

_Last updated 20 September 2018_

## DRAFT

These are notes for a future tutorial.

## Introduction

> Copy and paste from this "code column" to build a demo application. Read the adjacent column for explanation and details.

In this tutorial, we learn how to track visits to an ecommerce website. Ecommerce tracking allows you to measure the number of transactions and revenue that your website generates. More importantly, ecommerce tracking helps you identify marketing campaigns that result in highest revenue.

Google Analytics and Mixpanel are popular options for website tracking and analytics. Google Analytics primarily focuses on tracking page views to show usage of a website, though it can be configured to record user actions such as a visitor's progress through a sales funnel. Mixpanel focuses on tracking visitors rather than page views, which means once a visitor enters an email address or other identifying information, the visitor's page views and actions can be viewed in aggregate. Products such as Google Analytics and Mixpanel are designed to be feature-rich for non-programmers, with complex interfaces that require expertise to configure.

In this tutorial, we'll look at a "do-it-yourself" alternative for programmers that can simplify ecommerce tracking. We'll use the [Ahoy JavaScript analytics library](https://github.com/ankane/ahoy.js) in combination with a Rails application that uses the [Ahoy Ruby gem](https://github.com/ankane/ahoy). Rather than use a tool such as Google Analytics or Mixpanel, you can add the Ahoy JavaScript analytics script to any website and send tracking data to a Rails application. You can customize the integration as you wish and you won't pay for the service or rely on a third-party to store your data. You may also gather more data, since many users employ ad blockers that prevent Google Analytics from collecting data.

## Architecture

You can add the Ahoy JavaScript analytics script to any website (it doesn't have to be a Rails application). The Ahoy JavaScript library will send tracking data to any URL you set in the Ahoy configuration.

We will collect the tracking data by creating a Rails application that records the Ahoy API requests. We could create an API-only Rails application just to save the data to a database but in this case we'd like to visit the application and see reports.

## Scope and Tasks

The primary purpose of this tutorial is to introduce the concepts you'll need to understand the implementation of ecommerce tracking. To that end, we will build a simple generic website with HTML and CSS and add the Ahoy JavaScript analytics library.

To record the tracking data, we'll build a Rails application and add the [Ahoy Ruby gem](https://github.com/ankane/ahoy) that records Ahoy API event requests.

After we've confirmed that the Rails application captures data from the simple generic website, we'll build an ecommerce website in Rails and implement event tracking using the Ahoy API. With the experience you gain, you'll be able to integrate the Ahoy tracking library with any ecommerce website you build and capture the data with a Rails application.

You'll also have a solid understanding for integrating sophisticated ecommerce strategies such as retention marketing with the [Retention Rocket](https://www.retentionrocket.com/) platform. Retention Rocket uses a JavaScript library that is similar to the Ahoy library and sends event data to track and respond to events such as cart abandonment. Our tutorial [Retention Marketing with Retention Rocket](https://railsapps.github.io/tutorial-retention-marketing/) goes in depth to show how to reduce cart abandonment and increase site revenue.
