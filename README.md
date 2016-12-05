# pingpongfy-doc

## Introduction
PingPongfy is an open source project to manage your ping pong tables at work. Isnt annoying to waste time while waiting your turn to play? With Pingpongfy not only you will know in real-time the state of the table but also will get notifications (browser notifications, sounds, trumpets, goats yelling...) whenever table is free or you got a partner to play. Pingpongfy is **in development** right now.

### Product features
* Real time status of the pingpong table: it shows who is playing right now.
* Browser notifications: configurable notifications for following cases:
  * Table is free.
  * You got a slot and other user join the table.

## Architecture
Following image represents the architecture of the application:

![Architecture](https://s3.postimg.org/psw06okn7/architecture.png)

### Main components

In a nutshell:

* Serverless frontend: using cloudfront as cdn and s3 for serving html+js+css
* AWS Cognito: used for signing/login process. It provides security layer to apigateway and maybe also to the websocket service.
* Websocket service: **to be defined** (AWS IoT is top candidate for now). Manages ws connections and pushes messages to browsers whenever table is updated.
* Rest api: 
  * using apigateway to pre-process the request (caching, security). 
  * Requests are authenticated using the aws cognito authorizer.
* Storage:
  * RDS for relational data.
  * DynamoDb Table for status for the table: 
    * dynamodb is great storage for reacting over data changes. Hence, API is decoupled from message system.
    * dynamodb will invoke a lambda function whenever state of table changes.
  * Lambda:
    * invoked by dynamodb table changes will publis a message to SNS.


