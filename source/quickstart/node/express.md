---
title: Getting Started with Node.js on Aptible Using the Express Framework
subtitle: Deploy your Express app on Aptible in about 5 minutes
---

This guide will show you how to set up a Node.js app using the Express framework and the Mongoose ODM for Mongodb.

This guide assumes you have:

- An Aptible account
- An SSH key associated with your Aptible user account
- The Aptible command line tool installed

## 1. Provision Your App

Tell the Aptible API that you want to provision an application. Until you push code and trigger a build, Aptible uses this as a placeholder.

Use the `apps:create` command: `aptible apps:create $APP_HANDLE`

For example:

    aptible apps:create express-quickstart

## 2. Add a Git Remote

Add a Git remote named "aptible":

    git remote add aptible git@beta.aptible.com:$APP_HANDLE.git

For example:

    git remote add aptible git@beta.aptible.com:express-quickstart.git

## 3. Add a Procfile

A Procfile explicitly declares what processes we should run for your app.

A few guidelines:

1. The file should be named "Procfile": One word, capital "P", no extension.
2. Place the Procfile in the root of your repository.
3. Be sure to commit it to version control.

Here is a sample Procfile for a Node.js app:

    web: node server.js -p $PORT

> Note: Aptible uses Docker to build and run your app. If you do not include a Dockerfile in your repository, Aptible will attempt to build your app with the [tutum/buildstep](https://registry.hub.docker.com/u/tutum/buildstep/) image.

## 4. Provision a Database

By default, `aptible db:create $DB_HANDLE` will provision a 10GB PostgreSQL database.

For a Mongodb database, we will specify the `--type` option:

    aptible db:create $DB_HANDLE --type mongodb

`aptible db:create` will return a connection string on success. The host value is mapped to a private subnet within your stack and cannot be used to connect from the outside Internet. Your containerized app can connect, however.

Add the connection string as an environment variable to your app:

    aptible config:add DATABASE_URL=$CONNECTION_STRING

## 5. Connect the Database Using Mongoose

Instantiate the Mongoose connection with the $DATABASE_URL variable you loaded into your app's environment in Step 4.

```javascript
// in server.js
var mongoose = require('mongoose');
mongoose.connect(process.env.DATABASE_URL);
```

## 6. Deploy Your App

Push to the master branch of the Aptible git remote:

    git push aptible master

If your app deploys successfully, a message will appear near the end of the remote output with a default VHOST:

    VHOST express-quickstart.on-aptible.com provisioned.

In this example, once the ELB provisions you could visit express-quickstart.on-aptible.com to test out your app.
