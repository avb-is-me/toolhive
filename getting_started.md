# Getting Started with ToolHive

ToolHive is a powerful tool for running and managing MCP (Managed Container Platform) servers. This guide will walk you through the installation process, basic usage, and key features of ToolHive.

## Table of Contents

1. [Installation](#installation)
2. [Basic Usage](#basic-usage)
3. [Running an MCP Server](#running-an-mcp-server)
4. [Transport Modes](#transport-modes)
5. [Key Features](#key-features)

## Installation

To install ToolHive, follow these steps:

1. Download the latest release from the official ToolHive repository.
2. Extract the downloaded archive to a directory of your choice.
3. Add the ToolHive directory to your system's PATH.

Verify the installation by running:

```bash
thv version
```

## Basic Usage

ToolHive's main command is `thv`. Here are some basic commands to get you started:

```bash
# Show help
thv --help

# List available commands
thv

# Check for updates
thv update
```

## Running an MCP Server

To run an MCP server using ToolHive, use the `run` command:

```bash
thv run [flags] SERVER_OR_IMAGE [-- ARGS...]
```

Example:

```bash
thv run --name my-server --transport sse my-mcp-image:latest
```

This command will start an MCP server using the specified image and configuration.

## Transport Modes

ToolHive supports two transport modes:

1. **SSE (Server-Sent Events)**: 
   - Use `--transport sse` 
   - Suitable for real-time, one-way communication from server to client
   - Example: `thv run --transport sse --port 8080 --target-port 3000 my-mcp-image`

2. **STDIO (Standard Input/Output)**:
   - Use `--transport stdio` (default)
   - Suitable for simple, bidirectional communication
   - Example: `thv run --transport stdio my-mcp-image`

## Key Features

1. **Container Management**: ToolHive uses container runtime to manage MCP servers, providing isolation and portability.

2. **Automatic Updates**: ToolHive checks for updates on startup, ensuring you're always using the latest version.

3. **Permission Profiles**: Control the permissions granted to MCP servers using predefined or custom profiles.

4. **Environment Variables**: Easily set environment variables for your MCP servers using the `--env` flag.

5. **Volume Mounting**: Mount host directories into the container using the `--volume` flag.

6. **OIDC Authentication**: Enable OIDC validation for enhanced security using flags like `--oidc-issuer` and `--oidc-audience`.

7. **Registry Integration**: ToolHive can fetch server configurations from a registry, simplifying deployment and management.

8. **Foreground and Background Modes**: Run servers in the foreground for debugging or in the background for production use.

For more detailed information on these features and advanced usage, please refer to the specific documentation pages for each topic.