# GitHub copilot troubleshooying and documentation lab

This repository contains a collection of challenges to improve your skills with GitHub Copilot regarding troubleshooting, documentation and related topics.

## Pre-requisites

- [Visual Studio Code](https://code.visualstudio.com/) or any other editor that supports GitHub Copilot.
- [GitHub Copilot](https://copilot.github.com/) extensions installed.
- Java 17 or higher
- Maven

## Getting started

1. Clone this repository. 
2. Open the project in Visual Studio Code or your favorite editor.

## Challenges

### Understand the API

    This challenge is about understanding the API that you are going to test. You will need to document the API and understand the endpoints, request and response payloads, and the expected behavior of the API. Create also examples of curl commands to interact with the API.

    The output should be a markdown file with the API documentation.

**Swagger**

    Add Swagger to the project to document the API and test it.

**Code Documentation**

    Document all clases and methods with the necessary information to understand the code. Don't forget to add documentation to test clases

**Documentation**

    Update the API documentation with the new information. Generate two different markdown files on with the functional documentation and another with the technical documentation.


**Bonus**
  
    Translate the code to another languaje of your choice and test it.


# Testing Employee API with curl

## Get All Employees
```sh
curl -X GET http://localhost:8080/api/employees
```

## Get Employee by ID
```sh
curl -X GET http://localhost:8080/api/employees/{id}
```
Replace `{id}` with the actual employee ID.

## Create a New Employee
```sh
curl -X POST http://localhost:8080/api/employees -H "Content-Type: application/json" -d '{
  "name": "John",
  "surname": "Doe",
  "email": "john.doe@example.com"
}'
```

## Update an Existing Employee
```sh
curl -X PUT http://localhost:8080/api/employees/{id} -H "Content-Type: application/json" -d '{
  "name": "Jane",
  "surname": "Doe",
  "email": "jane.doe@example.com"
}'
```
Replace `{id}` with the actual employee ID.

## Delete an Employee
```sh
curl -X DELETE http://localhost:8080/api/employees/{id}
```
Replace `{id}` with the actual employee ID.
```