---
layout: post
title: "Diving into GraphQL: GraphQL vs REST"
---
This post briefly discusses the following:
1. What is GraphQL?
2. Difference of GraphQL conventions from REST
3. Why use GraphQL?

This article is a prequel to a series. The main series will tackle building an app with a software stack that utilizes GraphQL: React Native/Apollo (frontend) + Prisma/ElasticSearch (backend). 

## [](#header-2)What is GraphQL?

GraphQL have been circulating for a while now and the pool of enthusiasts is growing rapidly. The Tech giant Facebook developed this powerful tool to seamlessly accomodate billions of API calls a day. A number of renowned companies like Twitter, Github, and many others have adopted it since Facebook open sourced it.

GraphQL is simply a query language for APIs but implements a different architecture design from REST. It was built to address shortcomings of REST in terms of performance and flexibility.

## [](#header-2)Difference of GraphQL conventions from REST

#### 1. In REST, each resource is represented by an endpoint. In GraphQL, you can access different resources merged together from a single endpoint.
    

**The Requirement:** Getting user name and tweets from user
    
**REST** *requires accessing two endpoints to get the name of user and the tweets associated with the user.*
``` http 
GET /user/:id
GET /user/:id/tweets 
```


**GRAPHQL** *simply requests information from a single call.*
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



#### 2. In GraphQL, what you ask for is what you get.

Referring to the above query examples, let's take a look at the difference in responses.

*With* **REST**, *as mentioned earlier, we need to access two endpoints to get the data we want. Each endpoint returns the full schema of the resource.*
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
*If client only wants the user's name and the text and timestamp of the tweets, it is up to the client to extract wanted fields or furthermore, reconstruct the desired JSON response on client's end to feed into some view.*


**GRAPHQL***'s response only includes the fields you explicitly ask for.*
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


#### 3. GraphQL uses GraphQL operation types instead of relying on RESTful HTTP methods to perform API CRUD (create, retrieve, update, delete) operations. 
    
**REST***'s HTTP methods*
    
**GET** Retrieve a resource<br>
**POST** Create a resource<br>
**PUT** Update an existing resource<br>
**DELETE** Delete a resource
``` json
    GET /user/:id
    POST /user
    {
        "name": "Karen",
        "email": "kmb@gmail"
    }
    PUT /user/:id
    {
        "email": "kmbv@gmail"
    } 
    DELETE /user/:id        
```    

    
*In* **GRAPHQL***, the identity is different from how you fetch the data.*

To retrieve user information, **query** operation type is used.
    
``` graphql
query UserInfo($id: Int!) {
 user(id: $id) {
        name,
        email
 }
}
```

To create a new user, **mutation** operation type is used.

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
 
Update and delete operations uses **mutation** as well.
    
More information about query and mutation formats in this [documentation](http://graphql.org/learn/queries/). 



#### 4. Client can ask for the type of queries supported in GraphQL. 

The Type system in GraphQL describes the set of possible data you can query on a particular service. Queries are validated and executed against the schema. With REST, one way of implementing this is via the OpenAPI specification allowing developers to learn about the capabilities of each endpoint.

Below is an example of type query. Response shows all the fields available for querying.
``` graphql
{
  __schema {
    types {
      name
    }
  }
}
```
Response:
``` json
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "Query"
        },
        {
          "name": "User"
        },
        {
          "name": "Tweet"
        },        
        {
          "name": "String"
        },
        {
          "name": "Boolean"
        },
        ...,
        {
          "name": "Mutation"
        },
        {
          "name": "__Schema"
        },
        {
          "name": "__Type"
        },
        {
          "name": "__TypeKind"
        },
        {
          "name": "__Field"
        },
        ...        
      ]
    }
  }
}
```

#### 5. Now let's get into the Schema! GraphQL has a strongly typed Schema.

The schema is comprised of mostly object types which is represented as below:

``` graphql
type User {
  name: String!
  email: String!
}

type Tweet {
  text: String!
  timestamp: String!
  likes: [User]
  retweets: [User]
  writtenBy: User!  
}
```
Object types have fields. Fields are associate to types. Square brackets denote an array. Exclamation points at the end means it is non-nullable.

#### 6. GraphQL supports subscriptions!

Aside from CRUD operations, GraphQL implements another operation type called **subscription** which offers realtime app capabilites. In REST, there is no way to get notified with changes on resources unless you request information from the resource.

Take a look below at a subscription that will notify a user if new tweets have been posted.
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

## [](#header-2)Why use GraphQL?

* Eliminates multiple round trips thus decreasing delivery latency and increasing throughput.
* Reduces overhead since you exactly get what you've asked for.
* There is no need for API versioning. Data fetching is flexible as you simply ask for what you need.
* In GraphQL, the schema objects are strongly typed. Because of this, errors related to formats will be avoided.
* Supports real time data by leveraging the power of subscriptions.
* GraphQL implements an instrospection system which gives the client the means to retrieve the exact description of the data it can query against. If you want to know what fields you can select, the type query can serve as the documentation for the client.


Knowing all of these benefits, why not use GraphQL? I think it is gonna be worth one's time.















