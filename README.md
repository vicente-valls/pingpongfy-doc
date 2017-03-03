# pingpongfy-doc

## Introduction
PingPongfy is an open source project to manage your ping pong tables at work. Isn't a waste time waiting for your turn to play? With Pingpongfy not only you will know in real-time the state of the table but also you will get notifications when important events happen on the table (table is free, another person join table to play...). Pingpongfy is **in development** right now.

### Product features
* Real time status of the pingpong table: it shows who is playing right now.
* TBD auto-remove users when no one is playing on the table (either from video or sound stream).
* Browser notifications: configurable notifications for following cases:
  * Table is free.
  * You got a slot and other user join the table.

## Architecture
Following image represents the architecture of the application:

![Architecture](https://s27.postimg.org/3q3fwrqhv/pingpong_manager_2.png)

### Main components

In a nutshell:

* Serverless frontend: using cloudfront as cdn and s3 for serving html+js+css
* AWS Cognito: used for signing/login process. It provides security layer to apigateway and the AWS IoT (used by websockets).
* Websocket service: aws IoT manages ws connections and pushes messages to browsers whenever table is updated.
* Rest api: 
  * using apigateway to pre-process the request (caching, security). 
  * Requests are authenticated using the aws cognito authorizer.
  * Logic for apigateway can probably be encapsulated in a beanstalk app for effortless deployment.
* Storage:
  * RDS for relational data.
  * DynamoDb Table for status for the table: 
    * dynamodb is great storage triggering events on data changes. Hence, API is decoupled from message system.
    * dynamodb will invoke a lambda function whenever state of table changes.
  * Lambda:
    * invoked by dynamodb table changes will send a message to aws IoT (using https protocol).

### Codebase

* [Apigateway](https://github.com/vicente-valls/pingpongfy-apigateway)
* [API](https://github.com/vicente-valls/pingpongfy-api)
