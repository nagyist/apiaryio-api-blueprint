# Advanced API Blueprint Tutorial

Welcome to the advanced API Blueprint tutorial! This tutorial will take you through advanced topics like JSON Schema, request and response attributes, data structures and relation types.

This tutorial assumes that you have read the [API Blueprint Tutorial](./Tutorial.md).

## JSON Schema

Action request and response bodies can have associated schemas that describe the allowed structure of the body content. JSON bodies are typically described with [JSON Schema](http://json-schema.org/). Given a simple JSON response body we can describe the structure of the response with JSON Schema in a `+ Schema` section:

```apib
## Random Numbers [/random]

### Generate a random number [GET]
Generates and returns a random number.

+ Response 200 (application/json)

    + Body

            {
              "value": 12345
            }

    + Schema

            {
              "type": "object",
              "properties": {
                "value": {
                  "type": "number"
                }
              }
            }
```

The schema can describe the type of each member, which members are required, default values, and support a number of other advanced features. Below is a more complete example, taken from the API Blueprint tutorial blueprint:

```apib
### List All Questions [GET]
+ Response 200 (application/json)

    + Body

            [
              {
                "question": "Favourite programming language?",
                "published_at": "2014-11-11T08:40:51.620Z",
                "url": "/questions/1",
                "choices": [
                  {
                    "choice": "Swift",
                    "url": "/questions/1/choices/1",
                    "votes": 2048
                  }, {
                    "choice": "Python",
                    "url": "/questions/1/choices/2",
                    "votes": 1024
                  }, {
                    "choice": "Objective-C",
                    "url": "/questions/1/choices/3",
                    "votes": 512
                  }, {
                    "choice": "Ruby",
                    "url": "/questions/1/choices/4",
                    "votes": 256
                  }
                ]
              }
            ]

    + Schema

            {
              "$schema": "http://json-schema.org/draft-04/schema#",
              "type": "array",
              "items": {
                "type": "object",
                "required": [
                  "question", "published_at", "url", "choices"
                ],
                "properties": {
                  "question": {
                    "type": "string"
                  },
                  "published_at": {
                    "type": "string"
                  },
                  "url": {
                    "type": "string"
                  },
                  "choices": {
                    "type": "array",
                    "items": {
                      "type": "object",
                      "required": [
                        "choice", "url", "votes"
                      ],
                      "properties": {
                        "choice": {
                          "type": "string"
                        },
                        "url": {
                          "type": "string"
                        },
                        "votes": {
                          "type": "number"
                        }
                      }
                    }
                  }
                }
              }
            }
```

## Attributes

Another way of describing examples and the structure of your request and response content is by using [MSON](https://github.com/apiaryio/mson#readme). MSON, like API Blueprint, allows you to use human-readable plain text to describe things rather than formats designed for computer parsing like JSON or YAML. Where API Blueprint allows you to describe your API, MSON allows you to describe data structure.

MSON can be added to resources, actions, and individual requests or responses via an `+ Attributes` section. The random number generator API from above could look like this with MSON:

```apib
## Random Numbers [/random]

### Generate a random number [GET]
Generates and returns a random number.

+ Response 200 (application/json)

    + Attributes

        + value: 12345 (number)
```

When the above blueprint is parsed it will have a JSON body and JSON Schema example generated for it from the MSON attributes.

The polls API question resource can also be modeled using MSON. By default, the `Attributes` section is assumed to be an object, but in this case we explicitly list it as an `array` instead:

```apib
### List All Questions [GET]
+ Response 200 (application/json)

    + Attributes (array)

        + (object)
            + question: Favourite programming language? (required)
            + published_at: `2014-11-11T08:40:51.620Z` (required)
            + url: '/questions/1' (required)
            + choices (array, required)
                + (object)
                    + choice: 'Javascript' (required)
                    + url: '/questions/1/choices/1' (required)
                    + votes: 2048 (number, required)
```

Note: MSON is still considered a beta feature!

## Data Structures

Once you start using MSON, you may find yourself wanting to reuse certain commonly used or nested data structure components. This is possible with the `## Data Structures` section. Attributes sections can then reference the data structures defined in the Data Structures section by name.

Using the polls API question resource, we can split out the `Question` and `Choice` objects:

```apib
### List All Questions [GET]
+ Response 200 (application/json)

    + Attributes (array[Question])

## Data Structures

### Question
+ question: Favourite programming language? (required)
+ published_at: `2014-11-11T08:40:51.620Z` (required)
+ url: '/questions/1' (required)
+ choices (array[Choice], required)

### Choice
+ choice: 'Javascript' (required)
+ url: '/questions/1/choices/1' (required)
+ votes: 2048 (number, required)
```

## Relation Types

Actions in a blueprint can define a semantic domain-specific meaning by defining a relation type using a `+ Relation` section. This means that an action can have a specific meaning regardless of its URI or name, and allows clients to be built based on the domain of the API rather than specific URIs.

For example, in the polls API:

```apib
## Question [/question/{id}]
### View a Question Detail [GET]
+ Relation: question

### Update a Question [PUT]
+ Relation: question_update

### Delete a Question [DELETE]
+ Relation: question_delete

## Questions Collection [/questions]
### List All Questions [GET]
+ Relation: questions
```

A server or client implementation can now use this information to handle the specific API resource and action URIs.

## Conclusion

This tutorial has covered some advanced API Blueprint topics. For more in-depth information and other advanced topics, please see the [API Blueprint Specification](https://github.com/apiaryio/api-blueprint/blob/master/API%20Blueprint%20Specification.md).
