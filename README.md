# GraphQL & Gatsby
Short blurb here about the two


## Learning Objectives:
By the end of this lesson developers should be able to:
- Perform queries with GraphQL.
- Explain what GraphQL and it uses within the context of Gatsby.
- Render GraphQL queried data inside of Gatsby components.

## What is GraphQL
GraphQL is a new API standard that was developed and open-sourced by Facebook with the intent to be a more efficient, powerful and flexible alternative to REST.

Some consider GraphQL a database technology but this is incorrect, it is a **query language** for APIs.

GraphQL utilizes **declarative data fetching**, this allows a client to specify the exact data it wants from an API. Instead of utilizing multiple endpoints that all return static data structures, a GraphQL server only exposes a single endpoint and responds with the exact data the client is requesting.


## Querying with GraphQL
REST APIs data is accessed from specific endpoints and said data is returned in a clearly defined structure. This approach requires  REST data structures to be encoded in the URL that it is connected to.

Where as with GraphQL you able to access all data via one endpoint. All the client has to do is send a query requesting specific data from the single endpoint.

Take a look at this example:
```
Characters
| id  |    name     |      universe |
| --- | :---------: | ------------: |
| 1   | Mace Windu  |     Star Wars |
| 2   |   Teal'c    | Stargate SG-1 |
| 3   | Tyr Anasazi |     Andromeda |
```


To query for all the character's name you would do:
```
{
  allCharacters {
    name
  }
}
```
Lets break down this request:
1. `allCharacters` is called the `root field` of this query.
2. Everything that proceeds this field is called the `payload` of the query.
3. The only field that we are requesting from the API is `name` which is returned in the `payload`.

Your response would look like this:

```
{
  "data": {
    "allCharacters": [
      {
        "name": "Mace Windu"
      },
      {
        "name": "Teal'c"
      },
      {
        "name": "Tyr Anasazi"
      }
    ]
  }
}
```

Breaking down the response:
You can see in the response above that only the character's `name` was returned. Lets say we also wanted to request the `universe` that the characters are from and their name. How would we do that?

POSSIBLE YOU DO!
```
{
  allCharacters {
    name
    universe
  }
}
```


```
{
  "data": {
    "allCharacters": [
      {
        "name": "Mace Windu",
        "universe": "Star Wars"
      },
      {
        "name": "Teal'c",
        "universe": "Stargate SG-1"
      },
      {
        "name": "Tyr Anasazi",
        "universe": "Andromeda"
      }
    ]
  }
}
```

## Nesting Query Data
As we discussed before the power of GraphQL is not just in how simple it is to write a query, the power is in its flexibility. One great feature of GraphQL is `querying nested data`. Lets take our above example and add a second model, say we have a `Characters` and a `Weapons` model.
```
Characters
| id  |    name     |      universe |
| --- | :---------: | ------------: |
| 1   | Mace Windu  |     Star Wars |
| 2   |   Teal'c    | Stargate SG-1 |
| 3   | Tyr Anasazi |     Andromeda |
```

```
Weapons
| id  | character_id |         type |
| --- | :----------: | -----------: |
| 1   |      1       |  Light Saber |
| 2   |      2       | Ma'Tok Staff |
| 3   |      3       |    Gauss Gun |
```

Let say we want a `payload` that contains each Character's name and their Weapon of choice. How would we structure the request?

```
{
  allCharacters {
    name
    weapons {
        type
    }
  }
}
```
Yup, Its that simple! No need to send two separate request to different endpoints. We are able to just nest the data we want in out request and checkout how simplistic the response is:

```
{
  "data": {
    "allCharacters": [
      {
        "name": "Mace Windu",
        "weapons": [
            "type":  "Light Saber"
        ]
      },
      {
        "name": "Teal'c",
        "weapons": [
            "type":  "Ma'Tok Staff"
        ]
      },
      {
        "name": "Tyr Anasazi",
        "weapons": [
            "type":  "Gauss Gun"
        ]
      }
    ]
  }
}
```

How cool is that!? We are able to make one request to a single end point that returns data from two different models and nest the data neatly into one `payload`. Now all we have to do on the front-end is manipulate the `payload` and render the data however we would like.


## Queries with Arguments
Arguments in GraphQL come from the same principles as an argument in a function. Arguments passed to a query can help you manipulate the returned `payload`.

Going back to our Character example, say we want to return the character with the `id` equal to `1`.
```
{
  allCharacters(id: 1) {
    name
  }
}
```

With the query above we are passing the argument of `id` to the `root field` `allCharacters`, with the value of said argument being equal to `1`.

Here is the expected returned `payload`
```
{
  "data": {
    "allCharacters": [
      {
        "name": "Mace Windu"
      }
    ]
  }
}
```




## What Mutants? Yup, You Can Even Write Mutations
So unfortunately `mutations` will not allow you to become the next Witcher of GA. But they will allow you to put the C, U, and D in `CRUD`.

`Mutations` are used to make changes to data that is stored in the backend. `Mutations` are structured that same as normal queries but require you to start them with the `mutation` keyword.

I DO:
Create a new record
Lets say you want to create a new record.

```
mutation {
  createCharacter(name: "Kara Starbuck Thrace", universe: "Battlestar Galactica") {
      id
  }
}
```

`Mutations` also have a `root field`. In the example above it is called `createCharacter`, we pass it two arguments `name` and `universe`. The value of each argument in turn will be the data that is created in our database once the query is sent.

We are also requesting a `payload` from this `mutation` which demonstrates another powerful feature of GraphQL. Not only are we able to create a new character record for `Starbuck` but we are also able to query for her newly create ID all in one request. WOW!


## GraphQL in Gatsby
Start by cloning down this [repo](https://github.com/rc1336/first-gatsby-graphql-repo/tree/master) and navigate into the repo.

Then install these packages:
```npm i```

Create a `gatsby-config.js` file in the root directory of the project.

```
gatsby-config.js

module.exports = {
  plugins: [
    `gatsby-plugin-emotion`,
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

Once you have complete the steps mentioned above run:
`gatsby develop` in your terminal.

## First Query With GraphQL in Gatsby
When the app renders you will see that the site title `Sci-Fi` is awesome is in the `layout` component and the `about.js` page. It might not seem like that much work at the moment but imagine if this was an enterprise site and we need to change the title across the entire site. That would cause us to have to do a lot of manual lifting. But with GraphQL it is simple.

We can store common data such as page titles and such in the `siteMetadata` object. This object will be housed in the `gatsby-config.js` file. Let add the following to our `gatsby-config.js`:

```
module.exports = {
   siteMetadata: {
    title: `Title from siteMetadata`,
  },
}
```

Restart your development server.

## Page Queries
Now that we have added a page title our `siteMetadata` let try querying it in out `about.js` file using a `page query`:

```
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"
export default ({ data }) => (
    <Layout>
        <h1>Lets Talk About How {data.site.siteMetadata.title}</h1>
        <p>
            Sci-Fi is the best genre ever! And this site is going to tell you all about it! "Engage!"
    </p>
    </Layout>
)
export const query = graphql`
  query {
    site {
      siteMetadata {
        title
      }
    }
  }
`
```
Restart your development server and then navigate to the `about` page.

You will see that the title has been replaced with the title from the `siteMetadata` object.

## StaticQuery
`StaticQuery` allow non-page components; such as `layout.js` to retrieve `payloads` from GraphQL queries.

Replace the code in your `layout.js` file with the following:

```
import React from "react"
import { css } from "@emotion/core"
import { useStaticQuery, Link, graphql } from "gatsby"

import { rhythm } from "../utils/typography"
export default ({ children }) => {
  const data = useStaticQuery(
    graphql`
      query {
        site {
          siteMetadata {
            title
          }
        }
      }
    `
  )
  return (
    <div
      css={css`
        margin: 0 auto;
        max-width: 700px;
        padding: ${rhythm(2)};
        padding-top: ${rhythm(1.5)};
      `}
    >
      <Link to={`/`}>
        <h3
          css={css`
            margin-bottom: ${rhythm(2)};
            display: inline-block;
            font-style: normal;
          `}
        >
          {data.site.siteMetadata.title}
        </h3>
      </Link>
      <Link
        to={`/about/`}
        css={css`
          float: right;
        `}
      >
        About
      </Link>
      {children}
    </div>
  )
}
```

Adding this query will make the Main link text re-render to match the `siteMetadata` value.


Once you have viewed the changes in real time, lets go back and update the `siteMetadata` in the `gatsby-config.js` to the following:
```
  siteMetadata: {
    title: `Sci-Fi is Awesome!`,
  },
```

## Static Data vs GraphQL Data in Gatsby

## GraphiQL

## Source Plugins & Rendering Queried Data
