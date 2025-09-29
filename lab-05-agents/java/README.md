# GitHub copilot documentation lab

This repository contains a collection of challenges to improve your skills with GitHub Copilot to understand and distinguish between Copilot modes: Autocompletion, Chat, and Agent.

## Pre-requisites

- [Visual Studio Code](https://code.visualstudio.com/) or any other editor that supports GitHub Copilot.
- [GitHub Copilot](https://copilot.github.com/) extensions installed.
- Java 17 or higher
- Maven

## Getting started

1. Clone this repository. 
2. Open the project in Visual Studio Code or your favorite editor.

## Challenges

### Basic Copilot Autocomplete

    Open a file, such as EmployeeController.java

    Write:
    // Method to check if a number is prime  
    Watch Copilot suggest Java code and accept with Tab.

    Try another function, for instance:
    // Method to compute factorial  
    Edit the generated code and see if more relevant suggestions appear.

**Copilot Chat**

    Open Copilot Chat (Ctrl+Shift+' in VS Code).

    Ask questions like:
    “How can I inject a service into this controller?”

    “Why am I getting a NullPointerException on line 25?”

    Request examples, like:
    “Show me how to read a file line by line in Java.”

    Use chat to help debug a deliberate bug you add (e.g., null pointer, off-by-one error).

**Copilot Agent Mode Usage**

    Access Agent Mode via the VS Code command palette (Ctrl+Shift+P), then search for “Copilot Agent: ...”, or use the dedicated icon.

    Give high-level tasks such as:
    “Refactor all controllers to use constructor dependency injection.”

    “Generate unit tests for all public methods in the service package.”

    “Convert all Exception handling to use custom exceptions.”

    Review the steps or plan the Agent provides. Accept or adjust as needed.
    Approve and let the Agent make code changes across files as necessary.
    If possible, interact with the Agent to ask for explanations of changes.

    Try a secondary task, e.g.:
    “Add Javadoc comments to all public classes in the model package.”



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
