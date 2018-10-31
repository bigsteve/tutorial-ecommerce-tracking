
# Tutorial: Ecommerce Tracking with Ahoy

The tutorial [Ecommerce Tracking with Ahoy](https://railsapps.github.io/tutorial-ecommerce-tracking/) shows how to track visits to an ecommerce website so you can measure sales and marketing activity.

This repository contains the *source/index.html.md* file for the tutorial content as well as the [Slate](https://github.com/lord/slate) document generator.

## Prerequisites

To edit and publish the tutorial, you'll need:

 - Linux or OS X (Windows may work, but is unsupported)
 - Ruby (version 2.3.1 or newer)
 - Bundler

If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

## Download the Project

Copy the project files to your local environment using `git clone`.

```bash
$ git clone https://github.com/RailsApps/tutorial-ecommerce-tracking
$ cd tutorial-rocketsdk
```

You're in the tutorial project folder.

## View

Run the [Middleman](https://middlemanapp.com/) web server to view the tutorial in a web browser at [http://localhost:4567](http://localhost:4567).

```bash
$ bundle install
$ bundle exec middleman server
```

## Edit

Edit the file *source/index.html.md* to revise the tutorial. Use [Markdown](https://en.wikipedia.org/wiki/Markdown) formatting.

## Publish

Save your file, commit with git and push the changes to Github, and deploy it as [GitHub pages](https://pages.github.com/). See [Deploying Slate](https://github.com/lord/slate/wiki/Deploying-Slate) for details.

```bash
$ ./deploy.sh
```

Deploying the tutorial makes it available publicly at [https://railsapps.github.io/tutorial-ecommerce-tracking/](https://railsapps.github.io/tutorial-ecommerce-tracking/).

## Questions? Need Help? Found a bug?

Suggestions for the tutorial? Found an error? [Submit an issue](https://github.com/RailsApps/tutorial-ecommerce-tracking/issues).
