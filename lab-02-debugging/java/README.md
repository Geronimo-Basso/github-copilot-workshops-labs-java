
# GitHub Copilot Troubleshooting Lab Java

This repository contains a collection of challenges to improve your skills with GitHub Copilot regarding troubleshooting and debugging.

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

    This challenge is about understanding the API that you are going to test. You will need to understand the endpoints, request and response payloads, and the expected behavior of the API. Create also examples of curl commands to interact with the API.

### Debug and solve compile errors

    In this challenge, you will need to debug and solve compile errors in the project. You will need to use GitHub Copilot to help you identify the root cause of the errors and provide the necessary fixes.

    Expected result: an operative project with no compile errors.

**functional errors**

    Once application fully compiles you need to test all the endpoints and fix any functional errors that you find.


**Testing**

    Review the test cases and add new ones if necessary. Make sure that all the test cases pass.
    
# Testing Employee API with curl

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

## Get All Employees
```sh
curl -X GET http://localhost:8080/api/employees
```

## Get Employee by ID
```sh
curl -X GET http://localhost:8080/api/employees/{id}
```
Replace `{id}` with the actual employee ID.