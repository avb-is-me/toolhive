---
title: MCP Server Management
description: Learn how to manage MCP servers using ToolHive, including creating, starting, stopping, and monitoring servers.
---

# MCP Server Management

This guide explains how to manage MCP (Management Control Plane) servers using ToolHive. You'll learn how to create, start, stop, and monitor MCP servers, as well as troubleshoot common issues.

## Creating an MCP Server

To create a new MCP server, use the `thv run` command. This command allows you to specify various options for configuring your server.

Basic usage:

```bash
thv run [flags] SERVER_OR_IMAGE [-- ARGS...]
```

Example:

```bash
thv run --name my-mcp-server --port 8080 my-mcp-image:latest -- --config /path/to/config.yaml
```

### Important Flags

- `--name`: Specify a name for your MCP server (auto-generated if not provided)
- `--port`: Set the host port for the HTTP proxy to listen on
- `--transport`: Choose the transport mode (sse or stdio, default: stdio)
- `--target-port`: Set the container port to expose (for SSE transport)
- `--target-host`: Specify the host to forward traffic to (for SSE transport)
- `--permission-profile`: Set the permission profile (none, network, or path to JSON file)
- `--env` or `-e`: Pass environment variables to the MCP server
- `--volume` or `-v`: Mount volumes into the container
- `--secret`: Specify secrets to be fetched and set as environment variables

## Starting an MCP Server

When you run the `thv run` command, it automatically starts the MCP server. If a container with the specified name already exists and has the correct configuration, ToolHive will start that container. Otherwise, it will create a new container and start it.

## Stopping an MCP Server

To stop a running MCP server, use the `thv stop` command followed by the server name or container ID:

```bash
thv stop my-mcp-server
```

This command will gracefully stop the MCP server and its associated container.

## Monitoring MCP Servers

ToolHive provides several ways to monitor your MCP servers:

### Listing Running Servers

To list all running MCP servers, use the `thv list` command:

```bash
thv list
```

This will display information about all running MCP servers, including their names, container IDs, images, and status.

### Viewing Server Logs

To view the logs of a specific MCP server, use the `thv logs` command:

```bash
thv logs my-mcp-server
```

You can add the `-f` flag to follow the logs in real-time:

```bash
thv logs -f my-mcp-server
```

### Inspecting Server Configuration

To inspect the configuration of a running MCP server, use the `thv inspect` command:

```bash
thv inspect my-mcp-server
```

This will display detailed information about the server, including its container configuration, network settings, and mounted volumes.

## Troubleshooting

Here are some common issues and their solutions:

1. **Server fails to start**: 
   - Check the logs using `thv logs my-mcp-server` for error messages.
   - Ensure that the specified ports are not already in use.
   - Verify that the permission profile allows necessary access.

2. **Network connectivity issues**:
   - Make sure the correct `--transport` option is set (sse or stdio).
   - For SSE transport, check that `--target-port` and `--target-host` are correctly configured.
   - Verify that the permission profile allows network access if required.

3. **Volume mount problems**:
   - Ensure that the host paths specified in `--volume` flags exist and have correct permissions.
   - Check that the container paths are correct and writable by the MCP server process.

4. **Environment variable issues**:
   - Verify that all required environment variables are set using the `--env` flag.
   - For sensitive information, use the `--secret` flag to securely pass values.

5. **Permission denied errors**:
   - Review and adjust the permission profile to grant necessary access to resources.
   - Ensure that the container is running with the correct user permissions.

## Best Practices

1. Use descriptive names for your MCP servers to easily identify them.
2. Always specify a permission profile to limit the server's access to system resources.
3. Use environment variables or secrets to pass configuration rather than hardcoding values.
4. Regularly monitor server logs to catch and address issues early.
5. Keep your MCP server images up-to-date to benefit from the latest features and security patches.

By following this guide, you should be able to effectively manage your MCP servers using ToolHive. Remember to consult the ToolHive documentation for more detailed information on specific commands and features.