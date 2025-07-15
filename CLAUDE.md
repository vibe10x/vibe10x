# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Essential Commands

### Development Workflow

```bash
# Install dependencies (uses pnpm)
pnpm install

# Development build with watch mode
pnpm build

# Run linting
pnpm lint

# Run type checking
pnpm check-types

# Run tests
pnpm test

# Run a single test file
pnpm test <file-path>

# Format code
pnpm format

# Build extension VSIX package
pnpm vsix

# Build and install VSIX directly into VS Code
pnpm install:vsix

# Clean build artifacts
pnpm clean
```

### Running the Extension

1. **Development Mode (F5)**: Press F5 in VS Code to launch a new VS Code window with the extension running. Changes to webview will hot reload.

2. **VSIX Installation**: Use `pnpm install:vsix` to build and install the extension into your VS Code instance.

## High-Level Architecture

### Core Components

1. **Extension Core** (`src/`)

    - **Entry Point**: `src/extension.ts` - Activates and initializes all services
    - **ClineProvider**: `src/core/webview/ClineProvider.ts` - Central orchestrator managing webview, tasks, and state
    - **Task System**: `src/core/task/Task.ts` - Manages AI conversations, tool usage, and subtasks

2. **API Provider System** (`src/api/`)

    - Extensible architecture supporting 20+ AI providers (Anthropic, OpenAI, Gemini, Bedrock, etc.)
    - Each provider implements unified `ApiHandler` interface
    - Handles streaming, token counting, and model-specific configurations

3. **Webview UI** (`webview-ui/`)

    - React-based UI running in VS Code's webview
    - Communication via message passing between extension and webview
    - Components for chat, settings, history, and MCP management

4. **Mode System**

    - Different operational modes: Architect, Code, Ask, Debug, Orchestrator
    - Each mode has specific tool restrictions and behaviors
    - Extensible through custom modes

5. **MCP Integration** (`src/services/mcp/`)

    - Model Context Protocol for extending capabilities
    - Supports stdio, SSE, and HTTP transports
    - Dynamic tool and resource discovery

6. **Tool System** (`src/core/tools/`)
    - Implements various tools: file operations, command execution, browser actions
    - Tools are mode-restricted and validated before execution
    - Supports checkpoint creation for safe operations

### Key Services

- **Terminal Integration**: Executes shell commands with proper shell detection
- **Code Indexing**: Provides codebase search and semantic understanding
- **Checkpoint Service**: Git-based state saving/restoration
- **Marketplace**: Manages provider configurations and custom modes
- **Browser Integration**: Web content fetching capabilities

### Important Patterns

1. **Message Flow**: Extension ↔ Webview communication via typed messages
2. **State Management**: Centralized through `ContextProxy` and VS Code's `ExtensionContext`
3. **Provider Profiles**: Flexible configuration system for different API providers
4. **Task Stack**: Supports pausing tasks to handle subtasks
5. **Diff Strategies**: Smart file editing with multi-file search/replace

## Development Notes

- This is a monorepo using pnpm workspaces and Turbo
- The extension was recently renamed from "Roo Cline" to "Roo Code"
- Uses VS Code's webview API for the UI, not a regular web app
- Supports both sidebar and editor tab views
- Implements comprehensive error handling and user approval flows
- All file paths must be absolute when using tools

## Testing

- Unit tests use Vitest
- E2E tests in `apps/vscode-e2e/`
- Mock VS Code API available for testing
- Run tests with `pnpm test` or specific test files directly
