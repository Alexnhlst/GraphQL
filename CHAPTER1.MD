# Learning GraphQL :tada:

#### **What is GraphQL**

> "A query language for your API" - The GraphQL Foundation

GraphQL is a specification to query APIs, and provides a rever-side runtime to execute those queries. Based on the data model, GraphQL can return the data in exactly the same format as requested.

> "A language that doesn't affect the way you think about programming, is not worth knowing." - Alan Perlis

GraphQL changed the way how data was transferred between application in a fixed format, to a new approach to dynamically transfer data between a "frontend" and a "backend"

### Self-descriptive

REST is very minimalistic and does not enforce any types or schemas, and as a result, validation of input and output and documentation are complementary aspects of a REST API.
GraphQL has been designed with this in mind. This is based on a GraphQL schema, a strongly typed and object-oriented representation of the data model for the application.


<details>
    <summary>Example of GraphQL schema</summary>
    
   
    # A user
    type User {
        # Primary ID
        id: ID!
        name: String
        email: String# Date of Birth
        dob: Date
    }
        
    query {
        # gets a list of users 
        # Use offset for pagination
        # Limited to max 10 items 
        users(limit: int = 0, offset: int = 0): [User]
    }
    
</details>

### Usage driven and intuitive

REST APIs were designed with specific use-cases in mind, making the development of "backend" and "frontend" loosely coupled or even independent.
The design of a REST API is often directly linked to the data model of the database that it's reading and mutating, making it a mere abstraction layer over this database.
When the REST API was created to serve the UI of a specific frontend application, the design of the REST API (and consequentially the data model of the database) should always match that UI. When the UI changes, the data flow and the data model of that database no longer match. This can lead to the "n+1 problem".

### The N+1 Problem

This problem describes the cascade of independent requests caused due to the lack of data returned by the first response. For instance imagine a news feed backed by a relational database

<details>
    <summary>Relational DB</summary>

    - News_Feed with columns: (id, post_id, topics, added_date)
    - Posts with columns: (id, author_id, title, content, added_date)
    - Authors with columns: (id, name, image, added_date)
</details>

It is quite common to encounter REST endpoints that forward the data of the underlying database with little to no de-normalization. Although one might claim this is the best approach as it separates the concerns of each endpoint perfectly, it is detrimental to the performance and user-experience of modern web-apps. Most web-apps have complex data requirements usually depending on data that spans multiple tables or even datasources. If not addressed, this leads to a ping pong game between Server and Client

1. Should the backend grow with the requirements of each of it's clients?

2. Should the backend endpoints be changed as the client's requirements change?

3. Should the backend endpoints take configuration parameters to suit every client?

As we think through each of those options, we will notice that they all have their pros and cons.
Building a custom endpoint for a specific client allows you to fine-tune the backend for maximal performance. However, this will increase the number of endpoints to maintain.
Constantly adapting one endpoint to fit new requirements won't suffer the same issue but it will introduce a host of other questions:

- How do we migrate old clients to the newer version?

- Will we need to support both versions?

The last option combines the best of both worlds but introduces a level of indirection and requires a custom implementation.
GraphQL standardizes this approach and enables the client to exactly specify it's data requirements. The above example reduces to this one-roundtrip query:

```
query {
  news(topic: "graphql") {       
    id
    title
    content
    author {
      imageUrl
      name
    }
  }
}    
```

<details>
    <summary>JSON response</summary>

    {
        "data": {
            "news": [{
                "id": 111,
                "title": "Post 1",
                "content": "Content of Post 1",
                "author": {
                "id": 444,
                "imageUrl": "/image/author/444.png",
                "name": "Author 1"
                }
            }, {
                "id": 222,
                "title": "Post 1",
                "content": "Content of Post 1",
                "author": {
                "id": 555,
                "imageUrl": "/image/author/555.png",
                "name": "Author 1"
                }
            }, {
                "id": 333,
                "title": "Post 1",
                "content": "Content of Post 1",
                "author": {
                "id": 666,
                "imageUrl": "/image/author/666.png",
                "name": "Author 1"
                }
            }]       
        }
    }
</details>

### GraphQL vs REST vs GRPC-Web

- REST (with Swagger)
  
  REST is a very un-opinioated protocol. It has no schema and does not even specify what type of resources it supposed to return. By default it doesn't provide any structure, features for introspection or schemas for safety and correctness. The latter can be addressed by frameworks like swagger that allow to describe and validate the REST API using a schema.

What's good:
    - simple
    - un-opinionated
    - most proxies know how to cache REST APIs
  
The drawbacks:
    - no typesafety by default
    - not self documenting
    - does not solve the under- and over-fetching problem
    - does not solve the n+1 problem

- GRPC-Web

    GRPC-Web is a modern take of Remote Procedure Calls. It is a bi-directional, binary protocol on top of HTTP/2.It uses googles protobuf framework to describe the schema, deserialize and serialize messages into a binary format. The schema itself looks very similar to GraphQL.

    ```
    message Author {
    int32 id = 0;
    string  name = 1;
    string imageUrl = 2;
    }

    message Slice {
    int32 offset = 0;
    int32 limit = 1;
    }

    service Authors {
    // Sends a greeting
    rpc GetAuthors (Slice) returns (stream Author);
    }
    ```

What's good:

  - efficient two-way communication between client and server
  - typesafe
  - documentation can be generated

The drawbacks:

    - does not solve the under- and over-fetching problem 
    - does not solve the n+1 problem
    - most proxies can't cache GRPC request/responses

- GraphQL
  
  What's good:

  - typesafe  
  - effective communication between server and client thanks to client side query language
  - does not solve the n+1 problem
  - documentation can be generated
  - excellent developer tools
  - graphql clients can cache responses

The drawbacks:

  - does not use a compressed binary communication protocol by default (although serialization and compression has also it's own cost)
  - most proxies can't cache GraphQL request/responses
  - The extra level of indirection and schema validation can have a performance overhead