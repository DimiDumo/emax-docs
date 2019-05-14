---
layout: default
title: Microservices
parent: emax-engine
nav_order: 1
---

# Microservices

This page describes what a microservice is in the context of emax tools and how
to create a new one.

## What is a microservice

A microservice in the emax tools context can either be the direct access to one
collection of the database or a connection to an API. Each microservice should be
connected to the GraphQL server, to make the data it provides available to a
client through only one endpoint.

## How to create a new microservice

1. Use the [example
   microservice](https://gitlab.com/emaxdigital/emax-reporting/tree/develop/example-microservice)
 as a base.
   * `src/models/crud.model.js` describes the mongoose model of the collection
of the microservice.
   * `src/services/my-aggregation` describes an example of how to create an
aggregation.
   * When renaming or creating new services, it must be registered in `src/servies/index.js`.
2. Connect the microservice to the emax-engine server
   * Use the `_example-service` as base to create the new service that will
connect your microservice.
   * Rename the containing files to the name of your new service.
   * Register the new service at `src/services/index.js`.
 **Important**: The service must always be registered  *before* the graphql service.
3. Add the following lines to the `docker-compose.dev.rep.yml` file and replace 
*example-service* with the name of your microservice.  
```
  example-service:
    build:
      context: ./example-service
      dockerfile: Dockerfile.dev
    volumes: 
      - ./example-service:/usr/src/app
      - /usr/src/app/node_modules
    depends_on:
      - mongodb
```
4. Add the url of the mircoservice to emax-engine
   * In `config.default.json` of emax-engine add the url of your microservice
 inside the `microservice` object like follows: 
`"exampleService": "http://example-service:3030"` - where you should replace 
`example-service` and `exampleService` with the name you used in the docker-compose file.
   * Go to `src/services/example-service/example-service.class.js` and replace 
`exampleService` inside the setup function with the name of your microservice.
5. Restart the dev environment with `docker-compose -f docker-compose.dev.rep.yml up`
