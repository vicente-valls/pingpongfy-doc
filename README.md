# pingpongfy-doc

[![Build Status](https://travis-ci.org/vicente-valls/pingpongfy-doc.svg?branch=master)](https://travis-ci.org/vicente-valls/pingpongfy-doc)

## Introduction
PingPongfy is an open source project to manage your ping pong tables at work. 
Isn't a waste time waiting for your turn to play? 
With Pingpongfy not only you will know in real-time the state of the table 
but also you will get notifications when table is free.
Pingpongfy is **in development** right now.

### Product features
* Real time status of the pingpong table:
A [single-board computer](https://en.wikipedia.org/wiki/Single-board_computer) called *Whisperer* is responsible for keeping the status of the table updated. 
If the whisperer notices people are playing, it will call the API in order to update the table.
* Possible Notifications whenever table status changes (free/busy):
  * Browser notifications.
  * Slack notifications.
  * And many more upon further development.

API is defined by this [swagger](https://github.com/vicente-valls/blob/master/swagger.yml)

## AWS solution
Solution proposed using aws components:

### Architecture
Following image represents the architecture of the application:

![Architecture](https://github.com/vicente-valls/pingpongfy-doc/raw/master/img/main-schema.jpg
 "Architecture")

### Main components

In a nutshell these are the AWS components:

* Serverless frontend: using cloudfront and s3 for serving html+js+css files.
* Websocket service: aws IoT manages ws connections and pushes messages to browsers whenever table is updated.
* Rest API: 
  * using apigateway to pre-process the request (caching, security). 
  * Logic for apigateway can probably be encapsulated in a beanstalk app or in another lambda using serverless.
* Storage:
  * DynamoDb Table for status for the table: 
    * dynamodb is great storage triggering events on data changes. Hence, API is decoupled from message system.
    * dynamodb will invoke a lambda function whenever state of table changes.
* Table Status Update Services: they are responsible for notifying users of table status changes.
    * Table Status Update Handler: invoked by dynamodb table, PUT, PATCH, POST and DELETE operations 
        will send a message to the SNS topic with old and new record.
    * SNS - Update Table Topic: receives messages from the *Table Status Update Handler* 
        and will invoke the lambdas subscribed.  
    * IOT message publisher: will send a message to aws IoT (using https protocol).
    * Slack notifier: will send messages message to slack group.

### Codebase

* [Apigateway](https://github.com/vicente-valls/pingpongfy-apigateway)
