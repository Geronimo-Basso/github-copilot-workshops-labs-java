# GitHub copilot documentation lab

This repository contains a collection of challenges to improve your skills with GitHub Copilot regarding documentation and related topics.

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




# Bonus

## GitHub Copilot Best Practices
 
To make the most of GitHub Copilot in your development workflow, follow these best practices. For each, you will find a task to practice, as well as examples of both bad and good practices.
 

### Be Specific in Prompts
 
    Advice:
    The more context you provide in comments or code, the better Copilot’s suggestions will be.

    Task:
    Write a function that calculates the factorial of a number.

    First, use a vague comment or no comment.
    Then, write a clear comment describing exactly what you want, including parameter types and return value.

Examples:

**Bad Practice**

    // calculate  
    public int factorial(int n) {  
        // Copilot may generate irrelevant code  
    }  
    
**Good Practice**

    // Calculate the factorial of a non-negative integer n using recursion. Returns 1 if n is 0.  
    public int factorial(int n) {  
        // Copilot is more likely to generate correct and readable code  
    }  
 

**Review and Edit Suggestions**

    Advice:
    Always review Copilot outputs! Don’t accept code without reading it for logic, security, and style.

    Task:
    Generate a function with Copilot that parses a number from a string. Review the suggestion and edit it if it does not handle errors or edge cases correctly.

Examples:

**Bad Practice:**

    public int parseInt(String s) {  
        return Integer.parseInt(s); // No error handling  
    }  

**Good Practice:**

    public Optional<Integer> parseInt(String s) {  
        try {  
            return Optional.of(Integer.parseInt(s));  
        } catch (NumberFormatException e) {  
            return Optional.empty();  
        }  
    }  
 

### Use Incremental Prompts

    Advice:
    Break complex tasks into smaller parts and prompt Copilot for each. Don’t expect a multi-step solution from one prompt.

    Task:
    First, ask Copilot to implement user authentication logic in a single comment.
    Then, split the implementation into:

    a function for verifying a username,
    a function for password verification,
    and a function for combining the results.

Examples:

**Bad Practice:**

    // authenticate user  
    // Copilot might generate a mixing of logic and hard-to-debug code here  

**Good Practice:**

    // Verify that the username exists in the database  
    // Verify that the password matches the user's hash  
    // Return true only if both checks pass  
    // Copilot will suggest focused, maintainable code pieces for each step  


### Follow Coding Standards
 
    Advice:
    Make sure Copilot’s code matches your project’s style and naming conventions.

    Task:
    Accept a Copilot-generated method and adjust its formatting and naming to match the style of the surrounding code.

Examples:

**Bad Practice:**

    public int get_user_ID(String NAME){  
        // Inconsistent naming and formatting  
    }  

**Good Practice:**

    public int getUserId(String name) {  
        // Uses camelCase and consistent brace placement  
    }  
 

### Leverage Copilot for Learning, but Verify Outputs
 
    Advice:
    Use Copilot to explore alternatives and new patterns—but always check the logic.

    Task:
    Ask Copilot for two different ways to reverse a string. Test both methods and explain which you prefer and why.

Examples:

**Bad Practice:**
    Accept Copilot’s code without checking if it works for null or empty strings.

**Good Practice:**
    Test the suggestions with different inputs, and add null/empty checks as needed.


### Avoid Sensitive Data
 
    Advice:
    Never paste API keys, passwords, or confidential information in prompts.

    Task:
    Write a function that connects to a database. Use environment variables for credentials instead of embedding them in the code or prompts.

Examples:

**Bad Practice:**

    // Connect to db  
    // username: admin  
    // password: SuperSecret123  

**Good Practice:**

    // Connect to db using credentials from environment variables  
 

### Combine Copilot with Other Tools

    Advice:
    Use static analyzers, linters, and tests to ensure Copilot’s code is safe and well-structured.
    Task:
    Generate a Copilot function, then run your linter or static analyzer and correct any warnings or errors reported.
 

### Iterate with Feedback
 
    Advice:
    If Copilot’s suggestion isn’t right, revise your comment or try again for better results.
    Task:
    Write a prompt for a function, review the suggestion, and, if incorrect, refine your prompt until Copilot provides a suitable solution.
 