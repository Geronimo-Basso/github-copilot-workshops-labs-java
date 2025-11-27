# Lab 06 (Part 1) – Creating your own MCP server

## Getting Started

This lab includes a pre-configured Maven project structure in the `java/` folder with:
- `pom.xml` with MCP SDK dependencies
- Standard Maven folder structure (`src/main/java`, `src/test/java`)
- Package structure: `com.example.mcp`

**You will create the implementation files** (MCPServer.java, MCPClient.java) by following the instructions below.

---

## 1. What Is MCP?

The Model Context Protocol (MCP) is a standard that lets AI agents (clients) discover and call capabilities exposed by external processes (servers) in a consistent way. Instead of writing bespoke integrations for each tool or data source, you expose:

Think of MCP as "USB for AI tooling": a universal plug so clients (like GitHub Copilot, custom scripts, or other agent frameworks) can enumerate and invoke functionality.

### **🧱 High-Level MCP Architecture Overview**

MCP follows a **client-server model**, where:

- **MCP Hosts** run the AI models
- **MCP Clients** initiate requests
- **MCP Servers** serve context, tools, and capabilities

### **Key Components:**

- **Resources** – Static or dynamic data for models  
- **Prompts** – Predefined workflows for guided generation  
- **Tools** – Executable functions like search, calculations  
- **Sampling** – Agentic behavior via recursive interactions

### How MCP Servers Work

MCP servers operate in the following way:

- **Request Flow**:
    1. A request is initiated by an end user or software acting on their behalf.
    2. The **MCP Client** sends the request to an **MCP Host**, which manages the AI Model runtime.
    3. The **AI Model** receives the user prompt and may request access to external tools or data via one or more tool calls.
    4. The **MCP Host**, not the model directly, communicates with the appropriate **MCP Server(s)** using the standardized protocol.
- **MCP Host Functionality**:
    - **Tool Registry**: Maintains a catalog of available tools and their capabilities.
    - **Authentication**: Verifies permissions for tool access.
    - **Request Handler**: Processes incoming tool requests from the model.
    - **Response Formatter**: Structures tool outputs in a format the model can understand.
- **MCP Server Execution**:
    - The **MCP Host** routes tool calls to one or more **MCP Servers**, each exposing specialized functions (e.g., search, calculations, database queries).
    - The **MCP Servers** perform their respective operations and return results to the **MCP Host** in a consistent format.
    - The **MCP Host** formats and relays these results to the **AI Model**.
- **Response Completion**:
    - The **AI Model** incorporates the tool outputs into a final response.
    - The **MCP Host** sends this response back to the **MCP Client**, which delivers it to the end user or calling software.

---

## 2. Prerequisites & Environment Setup

### Java Requirements
- **JDK 17 or higher** (MCP Java SDK requires Java 17+)
- **Maven 3.8+**
- **IDE**: IntelliJ IDEA, Eclipse, or VS Code with Java extensions

### Maven Project Setup

The `lab-06-mcp/java/` folder already contains a complete Maven project with the MCP SDK dependency configured in `pom.xml`:

```xml
<dependencies>
    <dependency>
        <groupId>io.modelcontextprotocol</groupId>
        <artifactId>mcp-sdk</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>2.0.9</version>
    </dependency>
    <!-- Additional dependencies for JSON processing -->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.10.1</version>
    </dependency>
</dependencies>
```

No additional setup needed - the project is ready to use!

---

## 3. Building Your First MCP Server

### Challenge: Create MCPServer.java

Create a new Java class `MCPServer.java` inside the `lab-06-mcp/java/src/main/java/com/example/mcp/` folder.

Use GitHub Copilot to help you create the basic MCP server structure:

```java
package com.example.mcp;

import io.modelcontextprotocol.kotlin.sdk.server.Server;
import io.modelcontextprotocol.kotlin.sdk.server.ServerOptions;
import io.modelcontextprotocol.kotlin.sdk.server.StdioServerTransport;
import io.modelcontextprotocol.kotlin.sdk.Implementation;
import io.modelcontextprotocol.kotlin.sdk.server.transport.Transport;

public class MCPServer {
    private final Server server;

    public MCPServer() {
        // Create server with basic info
        Implementation implementation = new Implementation(
            "demo-mcp-server",
            "1.0.0"
        );
        
        ServerOptions options = new ServerOptions().setCapabilities(
            new ServerOptions.Capabilities()
                .setTools(new Object()) // Enable tools
                .setResources(new Object()) // Enable resources
        );
        
        this.server = new Server(implementation, options);
    }

    public static void main(String[] args) {
        MCPServer mcpServer = new MCPServer();
        
        // Use stdio transport
        Transport transport = new StdioServerTransport();
        
        System.err.println("MCP Server starting...");
        mcpServer.server.connect(transport);
    }
}
```

At this point the server has a name but exposes no tools or resources.

---

## 4. Expanding the Server: Tools & Resources

### Challenge: Add Math Tools

Now expand your `MCPServer.java` to register tools. Add a `registerTools()` method that creates the following tools:
- `add` - adds two numbers
- `subtract` - subtracts two numbers
- `multiply` - multiplies two numbers
- `divide` - divides two numbers (with error handling for division by zero)

Here's the pattern for registering tools:

```java
package com.example.mcp;

import io.modelcontextprotocol.kotlin.sdk.*;
import io.modelcontextprotocol.kotlin.sdk.server.*;
import io.modelcontextprotocol.kotlin.sdk.shared.*;

import java.util.List;
import java.util.Map;
import java.util.concurrent.CompletableFuture;

public class MCPServer {
    private final Server server;

    public MCPServer() {
        Implementation implementation = new Implementation(
            "demo-mcp-server",
            "1.0.0"
        );
        
        ServerOptions options = new ServerOptions().setCapabilities(
            new ServerOptions.Capabilities()
                .setTools(new Object())
                .setResources(new Object())
        );
        
        this.server = new Server(implementation, options);
        
        // Register tools
        registerTools();
        registerResources();
    }

    private void registerTools() {
        // Add tool
        server.addTool(
            "add",
            "Add two numbers",
            Map.of(
                "type", "object",
                "properties", Map.of(
                    "a", Map.of("type", "integer", "description", "First number"),
                    "b", Map.of("type", "integer", "description", "Second number")
                ),
                "required", List.of("a", "b")
            ),
            (arguments) -> {
                int a = ((Number) arguments.get("a")).intValue();
                int b = ((Number) arguments.get("b")).intValue();
                int result = a + b;
                
                return CompletableFuture.completedFuture(
                    new CallToolResult(
                        List.of(new TextContent(String.valueOf(result))),
                        false
                    )
                );
            }
        );

        // Subtract tool
        server.addTool(
            "subtract",
            "Subtract two numbers",
            Map.of(
                "type", "object",
                "properties", Map.of(
                    "a", Map.of("type", "integer", "description", "First number"),
                    "b", Map.of("type", "integer", "description", "Second number")
                ),
                "required", List.of("a", "b")
            ),
            (arguments) -> {
                int a = ((Number) arguments.get("a")).intValue();
                int b = ((Number) arguments.get("b")).intValue();
                int result = a - b;
                
                return CompletableFuture.completedFuture(
                    new CallToolResult(
                        List.of(new TextContent(String.valueOf(result))),
                        false
                    )
                );
            }
        );

        // Multiply tool
        server.addTool(
            "multiply",
            "Multiply two numbers",
            Map.of(
                "type", "object",
                "properties", Map.of(
                    "a", Map.of("type", "integer", "description", "First number"),
                    "b", Map.of("type", "integer", "description", "Second number")
                ),
                "required", List.of("a", "b")
            ),
            (arguments) -> {
                int a = ((Number) arguments.get("a")).intValue();
                int b = ((Number) arguments.get("b")).intValue();
                int result = a * b;
                
                return CompletableFuture.completedFuture(
                    new CallToolResult(
                        List.of(new TextContent(String.valueOf(result))),
                        false
                    )
                );
            }
        );

        // Divide tool
        server.addTool(
            "divide",
            "Divide two numbers",
            Map.of(
                "type", "object",
                "properties", Map.of(
                    "a", Map.of("type", "integer", "description", "Numerator"),
                    "b", Map.of("type", "integer", "description", "Denominator")
                ),
                "required", List.of("a", "b")
            ),
            (arguments) -> {
                int a = ((Number) arguments.get("a")).intValue();
                int b = ((Number) arguments.get("b")).intValue();
                
                if (b == 0) {
                    return CompletableFuture.completedFuture(
                        new CallToolResult(
                            List.of(new TextContent("Error: Division by zero")),
                            true
                        )
                    );
                }
                
                double result = (double) a / b;
                
                return CompletableFuture.completedFuture(
                    new CallToolResult(
                        List.of(new TextContent(String.valueOf(result))),
                        false
                    )
                );
            }
        );
    }

    private void registerResources() {
        // Dynamic greeting resource with URI template: greeting://{name}
        server.addResourceTemplate(
            "greeting://{name}",
            "Personalized greeting",
            (uri) -> {
                // Extract name from URI
                String name = uri.toString().replace("greeting://", "");
                String greeting = String.format("Hello, %s!", name);
                
                return CompletableFuture.completedFuture(
                    new ReadResourceResult(
                        List.of(new TextContent(greeting))
                    )
                );
            }
        );
    }

    public static void main(String[] args) {
        MCPServer mcpServer = new MCPServer();
        
        Transport transport = new StdioServerTransport();
        
        System.err.println("MCP Server starting with tools and resources...");
        mcpServer.server.connect(transport);
    }
}
```

---

## 5. Running & Inspecting Your Server

### Building the Project

From the `lab-06-mcp/java/` directory:

```bash
mvn clean package
```

### Running the Server

```bash
mvn exec:java -Dexec.mainClass="com.example.mcp.MCPServer"
```

Or run the JAR directly:

```bash
java -jar target/mcp-demo-1.0-SNAPSHOT.jar
```

### Testing with MCP Inspector

Use the MCP Inspector tool to test your server:

```bash
npx @modelcontextprotocol/inspector java -jar target/mcp-demo-1.0-SNAPSHOT.jar
```

---

## 6. Writing a Java MCP Client

### Challenge: Create MCPClient.java

Create a new Java class `MCPClient.java` in the same package that connects to your MCP server and tests all the tools.

Your client should:
1. Connect to the server via stdio transport
2. List all available tools and resources
3. Test the greeting resource with different names
4. Call each math tool with sample inputs
5. Demonstrate error handling (e.g., division by zero)

Here's the starter code:

```java
package com.example.mcp;

import io.modelcontextprotocol.kotlin.sdk.*;
import io.modelcontextprotocol.kotlin.sdk.client.*;
import io.modelcontextprotocol.kotlin.sdk.shared.*;

import java.util.List;
import java.util.Map;
import java.util.concurrent.CompletableFuture;

public class MCPClient {
    
    public static void main(String[] args) {
        try {
            runClient();
        } catch (Exception e) {
            System.err.println("Error running MCP client: " + e.getMessage());
            e.printStackTrace();
        }
    }
    
    private static void runClient() throws Exception {
        // Create client
        Client client = new Client(
            new Implementation("demo-client", "1.0.0"),
            new ClientOptions()
        );
        
        // Connect to server via stdio
        StdioClientTransport transport = new StdioClientTransport(
            new StdioClientTransport.StdioConfig(
                "java",
                List.of("-cp", "target/mcp-demo-1.0-SNAPSHOT.jar", 
                       "com.example.mcp.MCPServer")
            )
        );
        
        client.connect(transport).get();
        
        System.out.println("Connected to MCP Server\n");
        
        // List available tools
        System.out.println("== TOOLS ==");
        ListToolsResult toolsResult = client.listTools().get();
        for (Tool tool : toolsResult.getTools()) {
            System.out.println(" - " + tool.getName() + ": " + tool.getDescription());
        }
        
        // List available resources
        System.out.println("\n== RESOURCES ==");
        ListResourcesResult resourcesResult = client.listResources().get();
        for (Resource resource : resourcesResult.getResources()) {
            System.out.println(" - " + resource.getUri() + ": " + resource.getName());
        }
        
        // Test greetings
        System.out.println("\n== GREETINGS ==");
        for (String name : List.of("Alice", "Bob", "Charlie")) {
            try {
                ReadResourceResult result = client.readResource(
                    "greeting://" + name
                ).get();
                
                String content = ((TextContent) result.getContents().get(0)).getText();
                System.out.println(name + ": " + content);
            } catch (Exception e) {
                System.out.println("Failed greeting " + name + ": " + e.getMessage());
            }
        }
        
        // Test tool calls
        System.out.println("\n== TOOL CALLS ==");
        
        testToolCall(client, "add", Map.of("a", 1, "b", 7));
        testToolCall(client, "multiply", Map.of("a", 3, "b", 9));
        testToolCall(client, "divide", Map.of("a", 42, "b", 7));
        testToolCall(client, "subtract", Map.of("a", 100, "b", 42));
        
        // Error demo
        System.out.println("\n== ERROR DEMO (divide by zero) ==");
        try {
            testToolCall(client, "divide", Map.of("a", 1, "b", 0));
        } catch (Exception e) {
            System.out.println("Expected error: " + e.getMessage());
        }
        
        // Cleanup
        client.close();
    }
    
    private static void testToolCall(Client client, String toolName, Map<String, Object> args) {
        try {
            CallToolResult result = client.callTool(toolName, args).get();
            String output = ((TextContent) result.getContent().get(0)).getText();
            System.out.println(toolName + " " + args + " => " + output);
        } catch (Exception e) {
            System.out.println(toolName + " " + args + " => ERROR: " + e.getMessage());
        }
    }
}
```

### Run the Client

```bash
mvn exec:java -Dexec.mainClass="com.example.mcp.MCPClient"
```

---

## 7. Testing Your Implementation

Run your client to verify everything works:

```bash
mvn exec:java -Dexec.mainClass="com.example.mcp.MCPClient"
```

Expected output should show:
- ✓ Connected to MCP Server
- List of available tools (add, subtract, multiply, divide)
- Greeting resource results
- Math operation results
- Error handling demonstration

---

## 8. Using Your Server in GitHub Copilot Agent Mode

This lets GitHub Copilot Chat auto‑discover and invoke your MCP server as a tool source inside the editor.

### 8.1 What the `.vscode` Folder Is (and Why It Matters)

The `.vscode` folder:
- Lives at the *root of the workspace* (same level as `lab-06-mcp/`).
- Applies settings only to THIS project (not global VS Code settings).
- Is where Copilot (in Agent Mode) looks for per‑workspace MCP configuration.
- Can safely be committed (unless it contains secrets—avoid that).

You'll add:
1. `settings.json` – turns on MCP discovery
2. `mcp.json` – declares one or more MCP servers (your custom one + any external MCPs)

### 8.2 `settings.json` (Enable Discovery)

Create (or open) `.vscode/settings.json`:

```jsonc
{
  // Enable experimental MCP auto-discovery for Copilot Chat
  "chat.mcp.discovery.enabled": true
}
```

### 8.3 Understanding `mcp.json`

Create `.vscode/mcp.json`. This file is a registry of MCP servers the Copilot host can spawn.

### 8.4 Java MCP Server Configuration

**Windows:**
```jsonc
{
  "servers": {
    "java-mcp-demo": {
      "type": "stdio",
      "command": "java",
      "args": [
        "-cp",
        "${workspaceFolder}\\lab-06-mcp\\java\\target\\mcp-demo-1.0-SNAPSHOT.jar",
        "com.example.mcp.MCPServer"
      ]
    }
  },
  "inputs": []
}
```

**macOS / Linux:**
```jsonc
{
  "servers": {
    "java-mcp-demo": {
      "type": "stdio",
      "command": "java",
      "args": [
        "-cp",
        "${workspaceFolder}/lab-06-mcp/java/target/mcp-demo-1.0-SNAPSHOT.jar",
        "com.example.mcp.MCPServer"
      ]
    }
  },
  "inputs": []
}
```

**Using Maven Exec (Alternative):**
```jsonc
{
  "servers": {
    "java-mcp-demo": {
      "type": "stdio",
      "command": "mvn",
      "args": [
        "-f",
        "${workspaceFolder}/lab-06-mcp/java/pom.xml",
        "exec:java",
        "-Dexec.mainClass=com.example.mcp.MCPServer",
        "-q"
      ]
    }
  },
  "inputs": []
}
```

### 8.5 Start from VS Code

1. **Build your project first**: `cd lab-06-mcp/java && mvn clean package`
2. Open the `mcp.json` file in VS Code
3. Click the play ▶ icon next to `java-mcp-demo`
4. Open Copilot Chat and type: "Add 22 to 1 using the MCP server please"
5. You should see the `add` tool invoked → response: `23`

### 8.6 Try More Prompts

- "multiply 7 by 6"
- "divide 42 by 7"
- "subtract 100 from 150"
- "greet Maria using the greeting resource"

If a tool doesn't trigger: make the intent clearer ("Use the add tool to add 5 and 11").

---

# Lab 06 (Part 2) – Using already created MCPs

## Lab 06 (Part A) – Playwright MCP

This lab guides you through using a Playwright Model Context Protocol (MCP) integration (via GitHub Copilot Chat in Agent mode) to:
1. Open the NBA standings page and capture a screenshot (Goal 1).
2. Extract regular season standings into structured JSON (Goal 2).
3. Enrich the top teams with extra details from their team pages (Goal 3).

---

### 🚀 Getting Started - Install the Playwright MCP 

Navigate to `https://github.com/mcp/microsoft/playwright-mcp` and follow the steps to install the Playwright MCP depending on preferred IDE.

For VS Code, the Playwright MCP server is configured in `.vscode/mcp.json`:

```json
{
  "servers": {
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

Click "Start" next to the `playwright` server in the mcp.json file to launch it.

---

### Explore MCP offering

https://github.com/mcp

### 🎯 Goal 1 – Capture Standings

Ask the Agent to Navigate to `https://www.nba.com/standings`, handle consent, scroll fully, and save a full-page screenshot with the Playwright MCP, add more details like browser to use and such.

<details>
  <summary><strong>Show Prompt for Goal 1</strong></summary>

```text
Use the Playwright MCP with Microsoft Edge (headed). Go to https://www.nba.com/standings.
Maximize window.
Wait for network idle and the main standings table to be visible.
If a cookie/consent banner appears, accept or close it.
Scroll the full page to trigger any lazy-loaded content.
Return a short status JSON with:
{
  "ok": true/false,
  "title": "<page title>",
  "url": "<final url>",
  "detectedTables": <count>,
  "screenshot": "<path-to-screenshot>"
}
Also save a full-page PNG screenshot as ./artifacts/01_open_standings.png and include its path.
```
</details>

---

### 📊 Goal 2 – Extract Regular Season Standings

Objective: Convert the visible standings table(s) into a normalized JSON array, ask the Playwright MCP to get the standings in a JSON format.

<details>
  <summary><strong>Show Prompt for Goal 2</strong></summary>

```text
From https://www.nba.com/standings in the current session, extract the full regular season standings table into a clean JSON array.

Requirements:
One JSON object per team with fields:
{ "rank", "team", "conference", "wins", "losses", "winPct", "gamesBehind", "streak", "home", "away", "last10" }
Normalize team names (e.g., "L.A. Clippers" → "LA Clippers"). Parse numbers as numbers.
If the site separates by conferences, include the correct "conference" value.
Validate: no empty rows, rank is numeric and unique within a conference.

Output:
Return the JSON array (pretty-printed).
Save it to ./data/standings_raw.json and confirm file path in your reply.
```
</details>

---

### 🏟️ Goal 3 – Enrich Top Teams with Extra Details

Objective: For the top 4 teams per conference, navigate to each official NBA team page, extract one reliable attribute (arena OR coach; fallback to page URL), and merge back into enriched data.

You may first create `standings_enriched.json` (can just copy or augment `standings_raw.json`) if required by your MCP flow—some tools want a separate input file.

<details>
  <summary><strong>Show Prompt for Goal 3</strong></summary>

```text
Using ./data/standings_enriched.json:

For the top 4 teams per conference, navigate to their official team page from nba.com (e.g., via the Teams directory page).
For each team, attempt to extract a visible attribute to demo multi-page scraping (choose one you can reliably find):
"arena" name or "coach" name; if not found, collect "team page URL".
Add this field as teamDetail for each of the visited teams.

Return a compact JSON report with:
{
  "visitedTeams": <count>,
  "details": [ { "team": "...", "teamDetail": "...", "url": "..." } ],
  "notes": "any selectors used or fallbacks"
}

Also save merged data to ./data/standings_enriched_with_details.json.
```
</details>

---

## Lab 06 (Part B) – Using the Microsoft Learn MCP

This part shows how to use the Microsoft Learn MCP inside GitHub Copilot (Agent Mode) to research official Microsoft documentation with focused natural‑language prompts.

---

### 1. Install the Microsoft Learn MCP Server

Follow the installation instructions for your IDE here:

https://github.com/mcp/microsoftdocs/mcp#-installation--getting-started

### 2. Open Copilot Chat (Agent Mode)

1. Confirm the Microsoft Learn MCP server is running.
2. Open a new Copilot Chat session.
3. Use the prompt examples below—these are crafted to:
   - Force authoritative source usage.
   - Encourage deeper summaries.
   - Retrieve structured or multi‑section answers.

---

### 3. Prompt Set (Try These)

Use each prompt as-is first; then refine or chain follow‑ups.

#### Prompt 1 – End-to-End MCP Understanding
```
I need to understand MCP in GitHub Copilot end-to-end (agent mode, registries, server setup). search Microsoft docs.
```

#### Prompt 2 – Core Copilot Features by IDE
```
List the core GitHub Copilot features and which IDEs they work in. deep dive
```

#### Prompt 3 – Azure Functions (Full Lifecycle)
```
I need to understand Azure Functions end-to-end. search Microsoft docs and deep dive
```

#### Prompt 4 – How GitHub Copilot Works
```
How does GitHub Copilot work, search in Microsoft Docs.
```

---

### 4. Enhancement & Follow‑Up Ideas

After an initial answer, try follow‑ups like:

- "Summarize that into a 5‑bullet executive brief. fetch full doc where needed."
- "Provide a comparison table of Copilot feature support across VS Code, JetBrains, and Visual Studio."
- "List common Azure Functions triggers with one‑line use cases. search Microsoft docs."
- "Cite the exact Microsoft Docs URLs used."

---

### 5. Tips for Higher-Quality Responses

| Goal | Add These Phrases |
|------|-------------------|
| Force official sources | `search Microsoft docs`, `fetch full doc` |
| Depth / architecture | `deep dive`, `implementation details` |
| Validation | `cite sources`, `list doc URLs` |
| Structure | `return as JSON`, `provide a table`, `outline first` |

---

## Additional Resources

### MCP Java SDK Documentation
- [MCP Java SDK GitHub](https://github.com/modelcontextprotocol/java-sdk)
- [MCP Specification](https://spec.modelcontextprotocol.io/)

### Example Java MCP Servers
- Check the official MCP repository for community examples
- Explore Spring Boot integration possibilities for REST-based MCP servers

### Troubleshooting

**Issue: Server not starting**
- Ensure Java 17+ is installed: `java -version`
- Check your classpath includes all dependencies
- Verify the JAR was built: `mvn clean package`

**Issue: Copilot not discovering tools**
- Confirm `chat.mcp.discovery.enabled` is `true` in settings.json
- Check the server is running (look for play icon next to server name)
- Try restarting VS Code

**Issue: ClassNotFoundException**
- Run `mvn dependency:tree` to verify all dependencies are resolved
- Ensure the main class path matches your package structure

---

## Next Steps

1. **Extend Your Server**: Add more complex tools (file operations, API calls, database queries)
2. **Add Authentication**: Implement token-based auth for sensitive operations
3. **Create Specialized MCPs**: Build domain-specific servers (database access, cloud APIs, etc.)
4. **Integrate with Spring Boot**: Expose MCP alongside REST endpoints
5. **Explore Community MCPs**: Install and use pre-built MCP servers from the ecosystem
