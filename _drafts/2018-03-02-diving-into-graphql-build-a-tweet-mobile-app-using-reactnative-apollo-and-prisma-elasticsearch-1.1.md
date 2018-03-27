---
layout: post
title: "Diving into GraphQL: Build a tweet mobile app using React Native + Apollo and Prisma + ElasticSearch (PART 1)"
published: false
---

This article is a 3 part series:
1. What is GraphQL?
2. Building the Backend: Prisma + Elastic Search (coming soon)
3. Building the Frontend: React Native + Apollo (coming soon)

## [](#header-2)What is GraphQL?

GraphQL have been circulating for a while now and the pool of enthusiasts is growing rapidly. The Tech giant Facebook developed this powerful tool to seamlessly accomodate billions of API calls a day. A number of renowned companies like Twitter, Github, and many others have adopted it since Facebook open sourced it.

GraphQL is simply a query language for APIs but implements a different architecture design from REST. It was built to address shortcomings of REST in terms of performance and flexibility.

### Below highlights the main differences of REST and GraphQL conventions:
1. In REST, each resource is represented by an endpoint. In GraphQL, you can access different resources from a single endpoint.
    
    <u>Getting user name and tweets from user</u>
    
    **REST**
    ``` http 
GET /user/:id
GET /user/:id/tweets 
```
>Need to access two endpoints to get the name of user and the tweets associated with the user.

    **GRAPHQL**
    ``` graphql
GET or POST /graphql
query UserTweets($id: Int!) {
    user(id: $id) {
        name
        tweets {
            text
            timestamp
        }
    }
}
```
>Requested information included in a single call.


2. In GraphQL, what you ask for is what you get.

    Referring to the above query examples, let's take a look at the difference in responses.

    **REST**
    ``` json 
GET /user/:id
{
    id: 1
    name: "Karen",
    email: "kmb@gmail.com"
}
```
    ``` json
GET /user/:id/tweets
[
    {
        text: "First tweet",
        timestamp: "01-12-2018T00:01:01",
        likes: 14,
        retweets: 5
    },
    {
        text: "Second tweet",
        timestamp: "01-12-2018T00:02:01",
        likes: 2,
        retweets: 0
    }    
]   
```
>As mentioned earlier, we need to access two endpoints to get the data we want. Each endpoint returns the full schema of the resource. If client only wants the user's name and the text and timestamp of the tweets, it is up to the client to extract wanted fields or furthermore, reconstruct the desired JSON response on client's end to feed into some view.

    **GRAPHQL**
    ``` json 
{
    name: "Karen",
    tweets: [
        {
            text: "First tweet",
            timestamp: "01-12-2018T00:01:01"        
        },
        {
            text: "Second tweet",
            timestamp: "01-12-2018T00:02:01"        
        }
    ]    
}
```
>Referring to the previously shown GraphQL query, we specifically defined the fields we want from the endpoint so that is what we are exactly getting.


3. GraphQL uses GraphQL operation types instead of relying on RESTful HTTP methods to perform API CRUD (create, retrieve, update, delete) operations. In GraphQL, the identity is different from how you fetch the data.
    
    **REST**
    
    Retrieve -> **GET** Retrieve a resource
    ``` http
    GET /user/:id
```    
    Create -> **POST** Create a resource
``` json
    POST /user
    {
        "name": "Karen",
        "email": "kmb@gmail"
    }    
```    
    Update -> **PUT** Update an existing resource
``` json
    PUT /user/:id
    {
        "email": "kmbv@gmail"
    }
```    
    Delete -> **DELETE** Delete a resource    
    ``` http    
    DELETE /user/:id
```
> Each endpoint is associated with a method that will perform the requested action.
    
    **GRAPHQL**

    Retrieve -> Uses **query** operation type to get information about user.
    
    ``` graphql
query UserInfo($id: Int!) {
 user(id: $id) {
        name,
        email
 }
}
```
>**user** is associated with a field resolver that will perform the query and return results.

    Create -> Uses **mutation** operation type to create a new user.

    ``` graphql
mutation CreateUser($user: User!) {
  createUser(user: $user) {
        name
        email
  }
}
```
    Payload:
    ``` json
{
    "user": {
        "name": "Karen",
        "email": "kmb@gmail.com"
    }
}    
```
>**createUser** is again associated with a field resolver that will perform the creation of a new user object.
    
    Update, Delete -> Uses **mutation** as well.

4. GraphQL supports subscriptions!

    Aside from CRUD operations, GraphQL implements another operation type called **subscription**.

    ``` graphql
    subscription TweetSubscription($input: TweetInput) {
        tweetSubscribe(input: $input) {
            tweet {
                text ,
                timestamp,
                user {
                    name
                }
            }
        }
    }    
```


   




