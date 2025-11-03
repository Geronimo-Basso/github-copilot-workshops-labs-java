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

### 1.-  Basic Copilot Autocomplete

    Open a file, such as EmployeeController.java

    Write:
    // Method to check if a number is prime  
    Watch Copilot suggest Java code and accept with Tab.

    Try another function, for instance:
    // Method to compute factorial  
    Edit the generated code and see if more relevant suggestions appear.

### 2.- Copilot Chat

    Open Copilot Chat (Ctrl+Shift+' in VS Code).

    Ask questions like:
    “How can I inject a service into this controller?”

    “Why am I getting a NullPointerException on line 25?”

    Request examples, like:
    “Show me how to read a file line by line in Java.”

    Use chat to help debug a deliberate bug you add (e.g., null pointer, off-by-one error).

### 3.- Copilot Agent Mode Usage

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

### 4.- Custom Chat modes

    Objective: Experiment with Custom chat modes that define specific behaviors and tools for GitHub Copilot Chat, enabling enhanced context-aware assistance for particular tasks or workflows.

    Instructions:

    Open Copilot Chat (Ctrl+Shift+' in VS Code).
    Explore the different available chat modes and how they can help you in your workflow in this repository https://github.com/github/awesome-copilot/blob/main/README.chatmodes.md

    Create a custom mode 'Best Mode' in VS Code using the Command Palette (Ctrl+Shift+P) and selecting "Chat: New Mode file" and pasting the content from the Best Mode chat mode to get more precise and detailed answers, which you can find at https://github.com/github/awesome-copilot/blob/main/chatmodes/4.1-Beast.chatmode.md

    Try using a specific chat mode for a particular task, such as code generation or solving doubts.
    Compare the results obtained with different chat modes and adjust your approach as needed.

### 5.- Custom Prompt Files

**Objective**: Learn how to create reusable Markdown files (*.prompt.md) that define prompts for repetitive tasks such as explaining code, generating tests, or reviewing PRs. These files are typically stored in `.github/prompts/` within your repository. In VS Code, you can open Copilot Chat and execute the slash command `/<prompt-name>` that matches the file name (without extension). Prompts can request inputs using `${input:key:label}` and run in agent mode.

#### Part 1: Create an "Explain Java Code" Prompt

**Instructions**:
1. Create a file called `.github/prompts/explain-java.prompt.md` in your project.
2. Paste the following content:
   ```markdown
   ---
   mode: 'agent'
   description: 'Explain a Java method in a simple and structured way'
   ---
   
   Please explain the following Java code clearly for the selected audience.
   
   ☕ **Java code to explain**:
   ${input:code:Paste the Java code here}
   
   🎯 **Target audience**:
   ${input:audience:Who is this for? (beginner/intermediate/advanced)}
   
   Your explanation must include:
   - A short summary of what the code does
   - A step-by-step breakdown
   - Explanation of key Java concepts involved
   - One simple usage example
   - Common pitfalls or edge cases
   ```
3. Open the Copilot Chat panel in VS Code.
4. Type the slash command: `/explain-java`
5. Paste this example code when asked:
   ```java
   public int fibonacci(int n) {
       return n <= 1 ? n : fibonacci(n-1) + fibonacci(n-2);
   }
   ```
6. Set the audience to "beginner".

**✅ Checkpoint**: Copilot should produce a clear explanation with bullet points and an example.

#### Part 2: Create a "Code Review" Prompt

**Instructions**:
1. Create a file called `.github/prompts/review-java.prompt.md`
2. Paste the following content:
   ```markdown
   ---
   mode: 'agent'
   description: 'Perform a structured code review for Java code'
   ---
   
   Perform a technical review of the following Java code.
   
   ☕ **Code to review**:
   ${input:code:Paste your Java code here}
   
   ⚖️ **Focus areas**:
   ${input:criteria:readability, performance, security, maintainability, testing}
   
   Please respond with:
   - Findings grouped by each selected area
   - Potential risks and how to fix them
   - Recommended refactors or Java best practice improvements
   - Quick wins with priority levels (high/medium/low)
   ```
3. Go back to Copilot Chat and run: `/review-java`
4. Paste this code snippet (which has a common Java issue):
   ```java
   public List<Integer> addItems(List<Integer> items) {
       if (items == null) {
           items = new ArrayList<>();
       }
       for (int i = 0; i < 10; i++) {
           items.add(i);
       }
       return items;
   }
   ```

**✅ Checkpoint**: Copilot should flag potential issues like modifying the input parameter and suggest best practices.

#### Part 3: Create a "Generate Unit Tests" Prompt

**Instructions**:
1. Create a file called `.github/prompts/generate-tests-java.prompt.md`
2. Paste the following content:
   ```markdown
   ---
   mode: 'agent'
   description: 'Generate JUnit unit tests for a given Java method'
   ---
   
   Write a JUnit test suite for the following Java code.
   
   🧩 **Code under test**:
   ${input:code:Paste the Java method or class here}
   
   🧠 **Test strategy**:
   ${input:matrix:Describe the edge cases, invalid inputs, and expected failures}
   
   Requirements:
   - Use JUnit 5 conventions
   - Cover success, edge, and failure scenarios
   - Use clear and descriptive test names
   - Include minimal setup and teardown (@BeforeEach, @AfterEach if needed)
   - Highlight any missing test coverage areas
   ```
3. Run in Copilot Chat: `/generate-tests-java`
4. Paste a method such as:
   ```java
   public double divide(double a, double b) {
       return a / b;
   }
   ```
5. Describe matrix: "zero division, negative numbers, large numbers"
6. Save the result into a file: `DivideTest.java`
7. Run the tests:
   ```sh
   mvn test
   ```
   or
   ```sh
   gradle test
   ```

**✅ Checkpoint**: Copilot should produce a full JUnit test class with at least 3-4 test methods.

#### Part 4: Best Practices

- Always store prompt files in `.github/prompts/`
- Use meaningful names: `explain-java.prompt.md` → `/explain-java`
- Keep prompts short, structured, and action-oriented
- Add the `description` field in YAML front-matter — it appears in Copilot Chat suggestions
- Review prompt files via pull requests, just like code

#### Optional Challenges

1. Create an **onboarding prompt** that generates a 5-day learning plan for a new Java developer joining your project.
2. Create a **refactor prompt** that suggests Java best practice improvements for any file.
3. Create a **documentation prompt** that generates comprehensive Javadoc comments for the `EmployeeController.java` class.

### 6.- Computer Vision in multimodal models

    Objective: Explore the capabilities of multimodal models that can process and understand both text and images.

    Instructions:

    Open Copilot Chat (Ctrl+Shift+' in VS Code).

    Add the image EmployeeManagementUI.png located in this folder to the chat by dragging and dropping it into the chat window.

    Ask Copilot to implement a web-based user interface for the Employee Management System based on the provided image.

    Review the suggestions and code generated by Copilot.
    Make sure the code is correct and functional.
    
# 🎁 Bonus Track

In this section, you’ll explore **GitHub Copilot Coding Agent** in action. Follow these steps to try it out:  

1. **Download** the following repository: *[https://github.com/Geronimo-Basso/github-coding-agent-demo]*
2. **Create** a new Git repo with the code and upload it to GitHub.
3. **Explore** the repository briefly — you can even ask Copilot directly on GitHub to explain parts of the code.  
4. **Create a new issue** in the repository. We suggest naming it:

   You can structure the issue like this (or adapt it as you prefer):  

   **Summary**  
   Add UI controls and filtering logic so users can filter NBA players by height and weight in the Streamlit interface.  

   **Motivation**  
   Users can currently filter by name, team, school, country, position, and draft year. Adding height and weight filters will make it easier to search by physical attributes.  

   **Acceptance Criteria**  
   - Add height and weight sliders (min/max or range) to the sidebar.  
   - Filter results update immediately when adjusted.  
   - Include only players whose values fall within the selected range.  
   - Display units clearly (e.g., inches, pounds).  
   - Handle edge cases (invalid ranges, missing data, etc.).  

   **Implementation Notes**  
   - Check `nba-mock-data/players.csv` for column names and units.  
   - Filtering happens client-side since the CSV is loaded in memory.  
   - Add proper validation and unit labels in the UI.  
   - Update the README with details about the new filters.  

4. **Assign the issue** to **GitHub Copilot Agent**.  
5. **Observe and review** how Copilot handles the implementation.  
6. **Merge** the changes once ready — and don’t stop there! Try asking Copilot for more improvements or creative additions.  

# 🎁 Bonus Track II

In this section, you'll explore **GitHub Copilot CLI** in action. Follow these steps to try it out:

## Usage Modes

GitHub Copilot CLI can be used in two modes:

**Interactive mode**: Start an interactive session by using the `copilot` command. This is the default mode for working with the CLI.

In this mode, you can prompt Copilot to answer a question, or perform a task. You can react to Copilot's responses in the same session.

**Programmatic mode**: You can also pass the CLI a single prompt directly on the command line. You do this by using the `-p` or `--prompt` command-line option. To allow Copilot to modify and execute files you should also use one of the approval options. For example:

```bash
copilot -p "Show me this week's commits and summarize them" --allow-tool 'shell(git)'
```

## Prerequisites
- Node.js v22 or higher
- npm v10 or higher
- (On Windows) PowerShell v6 or higher
- An active Copilot subscription. See [Copilot plans](https://github.com/features/copilot)
- If you have access to GitHub Copilot via your organization or enterprise, you cannot use GitHub Copilot CLI if your organization owner or enterprise administrator has disabled it in the organization or enterprise settings

## Installation
1. **Install globally with npm**:
   ```sh
   npm install -g @github/copilot
   ```

2. **Launch the CLI**:
   ```sh
   copilot
   ```
   On first launch, you'll be greeted with an animated banner! If you'd like to see this banner again, launch copilot with the `--banner` flag.

3. **Authenticate**:
   If you're not currently logged in to GitHub, you'll be prompted to use the `/login` slash command. Enter this command and follow the on-screen instructions to authenticate.

   **Alternative: Authenticate with a Personal Access Token (PAT)**
   - Visit [https://github.com/settings/personal-access-tokens/new](https://github.com/settings/personal-access-tokens/new)
   - Under "Permissions," click "add permissions" and select "Copilot Requests"
   - Generate your token
   - Add the token to your environment via the environment variable `GH_TOKEN` or `GITHUB_TOKEN` (in order of precedence)

4. **Navigate** to this repository in your terminal.

5. **Use GitHub Copilot CLI** to generate code or fix issues. For instance, you can ask to enhance the Employee Management UI created in Challenge 5, for example:
   ```sh
   copilot "add search filters"
   ```
   
6. **Try asking for suggestions**. For instance, you can ask:
   ```
   Suggest improvements for employee_routes.py
   ```
   Review the suggestions provided by Copilot and apply the ones you find most useful.

7. **Ask Copilot to create an issue for you on GitHub.com**. For example:
   ```
   Raise an improvement issue in <your-org>/<your-repo>. Add comprehensive unit tests for employee_controller.py to improve code coverage and ensure all CRUD operations are properly tested.
   ```
   Replace `<your-org>/<your-repo>` with your actual GitHub repository. Copilot will help you draft and create an issue directly in your repository with appropriate details and acceptance criteria.

## Expected API Endpoints (once fixed)
    GET /employees - List all employees
    POST /employees - Add a new employee
    DELETE /employees/<id> - Delete an employee by ID
    PUT /employees/<id> - Update an employee by ID

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
