graph TB
    subgraph Client ["Client (Claude Code/Claude API)"]
        LLM["Claude LLM"]
        SDK["MCP Client SDK"]
    end

    subgraph Transport["Transport Layer"]
        JSON["JSON-RPC 2.0"]
        STDIO["stdio/HTTP/SSE"]
    end

    subgraph Server ["MCP Server (Local/Remote)"]
        Handler["Request Handler"]
        Resources["Resources<br/>Static Data"]
        Tools["Tools<br/>Callable Functions"]
        Prompts["Prompts<br/>Templates"]
    end

    subgraph Tools_Detail ["Tool Example"]
        ToolReq["Tool Call Request"]
        ToolExec["Execute Function"]
        ToolRes["Return Result"]
    end

    subgraph Resources_Detail ["Resource Example"]
        ResReq["Resource Request"]
        ResRead["Read File/API"]
        ResRes["Return Content"]
    end

    LLM -->|Uses tools & context| SDK
    SDK -->|Sends requests| JSON
    JSON -->|Over protocol| STDIO
    STDIO -->|Receives at| Handler

    Handler -->|Manages| Resources
    Handler -->|Manages| Tools
    Handler -->|Manages| Prompts

    Tools -->|Implements| Tools_Detail
    Resources -->|Implements| Resources_Detail

    ToolReq -->|List/Call| ToolExec
    ToolExec -->|Executes| ToolRes
    ToolRes -->|Returns via| JSON

    ResReq -->|Fetch| ResRead
    ResRead -->|Queries| ResRes
    ResRes -->|Returns via| JSON

    JSON -->|Response| SDK
    SDK -->|Context back to| LLM

    style Client fill:#e1f5ff
    style Server fill:#f3e5f5
    style Transport fill:#fff3e0

How MCP Works:

1. Client (Claude) – Makes requests for external tools/resources through the MCP Client SDK
2. Transport – JSON-RPC 2.0 messages sent over stdio, HTTP, or SSE
3. Server – Listens and responds with:
  - Tools – Functions Claude can call (execute bash, query DB, API calls, etc.)
  - Resources – Contextual data Claude can read (files, docs, real-time data)
  - Prompts – Custom instructions/templates for specific tasks
4. Response Flow – Server executes requests and sends results back via JSON-RPC
5. Feedback Loop – Claude receives context and makes informed decisions

Common MCP Use Cases:
- Connect Claude to your codebase, APIs, or databases
- Add custom tools (git commands, deployments, testing)
- Provide dynamic context (logs, metrics, documentation)
- Extend Claude Code capabilities
