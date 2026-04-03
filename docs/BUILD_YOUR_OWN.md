# Build Your Own MCP Server with CCKit

This guide walks you through creating a custom MCP server for your APIs using CCKit.

## Prerequisites

- Node.js v20+
- npm
- Understanding of your API
- 1-2 hours

## Step 1: Create Project Structure

```bash
mkdir my-custom-mcp-server
cd my-custom-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk axios zod dotenv typescript ts-node @types/node
```

## Step 2: Create TypeScript Configuration

Create `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

## Step 3: Create Your MCP Server

Create `src/index.ts`:

```typescript
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio";
import { Client } from "@modelcontextprotocol/sdk/client/index";
import {
  Tool,
  TextContent,
  ToolUseBlock,
} from "@modelcontextprotocol/sdk/shared/messages";
import { z } from "zod";
import axios from "axios";

const client = new Client({
  name: "my-custom-tool",
  version: "1.0.0",
});

// Define your tools here
const tools: Tool[] = [
  {
    name: "get_data",
    description: "Fetch data from your API",
    inputSchema: {
      type: "object" as const,
      properties: {
        endpoint: {
          type: "string",
          description: "API endpoint to call",
        },
      },
      required: ["endpoint"],
    },
  },
];

// Handle tool calls
async function handleToolCall(
  toolName: string,
  toolInput: Record<string, unknown>
): Promise<string> {
  if (toolName === "get_data") {
    try {
      const response = await axios.get(
        `https://your-api.com/${toolInput.endpoint}`
      );
      return JSON.stringify(response.data);
    } catch (error) {
      return `Error: ${error}`;
    }
  }
  return "Tool not found";
}

// Main initialization
async function main() {
  const transport = new StdioClientTransport();
  await client.connect(transport);

  // Register tools
  console.error("MCP Server started");
}

main().catch(console.error);
```

## Step 4: Add Build Script

Update `package.json`:

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js"
  }
}
```

## Step 5: Build and Test

```bash
npm run build

# Test by copying to Claude Code
cp dist/index.js ~/.fortuenti/mcp-servers/my-custom/
```

## Step 6: Register with Claude Code

Update `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "my-custom": {
      "type": "stdio",
      "command": "node",
      "args": ["/Users/ricardoiguaran/.fortuenti/mcp-servers/my-custom/index.js"]
    }
  }
}
```

## Step 7: Restart Claude Code and Test

```bash
# Close Claude Code completely
fortuenti-code
```

Your custom tools should now be available in Claude Code!

## Real Example: API Integration

Here's a real example that integrates with a REST API:

```typescript
const tools: Tool[] = [
  {
    name: "list_workflows",
    description: "List all workflows in your system",
    inputSchema: {
      type: "object" as const,
      properties: {
        limit: {
          type: "number",
          description: "Max results to return",
        },
      },
      required: [],
    },
  },
  {
    name: "create_workflow",
    description: "Create a new workflow",
    inputSchema: {
      type: "object" as const,
      properties: {
        name: { type: "string" },
        description: { type: "string" },
      },
      required: ["name"],
    },
  },
];
```

## Debugging

If your server doesn't show up:

1. **Check the logs:**
   ```bash
   npm run build
   node dist/index.js
   ```

2. **Verify settings.json** syntax is valid JSON

3. **Check file permissions:**
   ```bash
   chmod +x ~/.fortuenti/mcp-servers/my-custom/index.js
   ```

4. **Restart Claude Code completely**

## Next Steps

- Add error handling and validation
- Add authentication (API keys, OAuth)
- Implement logging
- Add unit tests
- Share your server with others!

## Resources

- [MCP Documentation](https://modelcontextprotocol.io)
- [Node.js MCP SDK](https://github.com/anthropics/mcp-sdk-js)
- [Examples](../examples/)

---

**Made with CCKit**
