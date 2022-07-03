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
