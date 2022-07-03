# Learning GraphQL :tada:

## Chapter 2 - Hello GraphQL

### Table of Contents
1. [First query](#first-query)
2. [Named queries](#named-queries)
3. [Variables](#variables)
4. [Multiple queries and renaming result fields](#multiple-queries-and-renaming-result-fields)
5. [Fragments](#fragments)
6. [Union types and interfaces](#union-types-and-interfaces)
    - [Interfaces](#interfaces)
    - [Discriminated unions](#discriminated-unions)
7. [Pagination](#pagination)
8. [Mutations](#mutations)
9. [GraphQL queries over HTTP](#graphql-queries-over-http)
---
## First query

GraphQL queries look a little like JSON

```
query {
    viewer {
        login
    }
}
```

<details>
    <summary>Response</summary>

    {
        "data": {
            "viewer": {
                "login": "nikhedonia",
            }
        }
    }

</details>

The shape of the request matches the shape of the response. GraphQL fills in the values for the keys requested. This shape-matching is powerful feature of GraphQL. Another powerful feature is it's type system.
In GraphQL every field has a type associated and can be inspected in GraphiQL. 
All field declaration are considered optional by default.
The documentation is generated directly from the schema.

```
# A user is an individual's account on GitHub ...
type User {
    login: String! # login is of type String. ! means it is never null
    email: String # email might be null
    # more fields...
}

type Query {
    user(login: String!): User
    viewer: User # The currently authenticated user
    # more fields...
}
```

---

## Named queries

It is highly recommended to name the queries. Although the name is optiona, it is important for code generators and clients. Most clients use the name for caching purposes.

```
query GetUser {
  user(login: "leebryon") {
    login
    name
    bio
  }
}
```

---

## Variables

Variables use special syntax. Their names start with $ sign and must be defined in the query arguments

```
query GetUser ($login: String!) {
    user(login: $login) {
        login
        name
        bio
    }
}
```

---

## Multiple queries and renaming result fields

GraphQL allows to combine multiple queries into one. However, this can may lead to naming conflicts. To resolve this issue GraphQL allows re-mapping of result fields.

```
query GetOrganizations {
  facebook: organization(login: "facebook") {
    login
    description
  }

  microsoft: organization(login: "microsoft") {
    login
    description
  }
}
```

---

### Fragments

GraphQL queries can get quite large and there a lot of good reasons to re-use "selections".
A fragment allows to "factor out" the common fields of a query.

```
query GetOrganizations {
  facebook: organization(login: "facebook") {
    ...commonOrgInfo
    # more fields
  }

  microsoft: organization(login: "microsoft") {
    ...commonOrgInfo
    # more fields
  }
}

fragment commonOrgInfo on Organization {
  login
  description
}
```

---