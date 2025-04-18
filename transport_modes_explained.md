---
title: Transport Modes Explained
description: An overview of the different transport modes available in ToolHive and how to use them
---

# Transport Modes Explained

ToolHive supports different transport modes for communication between the client and MCP server. This document explains the available transport modes, their use cases, and how to configure them.

## Available Transport Modes

ToolHive currently supports two transport modes:

1. STDIO (Standard Input/Output)
2. SSE (Server-Sent Events)

## STDIO Transport Mode

The STDIO transport mode uses standard input and output streams for communication between the client and the MCP server.

### When to use STDIO

- For simple, local communication scenarios
- When you want to pipe input/output directly to/from the MCP server
- For debugging and testing purposes

### Configuring STDIO Transport

To use STDIO transport, set the `--transport` flag to `stdio` when running an MCP server:

```bash
thv run --transport stdio your_mcp_server_image
```

## SSE Transport Mode

The SSE (Server-Sent Events) transport mode uses HTTP long-polling to enable real-time, unidirectional communication from the server to the client.

### When to use SSE

- For real-time updates from the server to the client
- When you need to handle multiple concurrent connections
- For scenarios requiring a persistent connection with low latency

### Configuring SSE Transport

To use SSE transport, set the `--transport` flag to `sse` when running an MCP server:

```bash
thv run --transport sse --port 8080 --target-port 3000 --target-host localhost your_mcp_server_image
```

Additional SSE-specific flags:
- `--port`: The port for the HTTP proxy to listen on (host port)
- `--target-port`: The port for the container to expose
- `--target-host`: The host to forward traffic to (default is "localhost")

## Transport Factory

ToolHive uses a transport factory to create the appropriate transport based on the provided configuration. The factory is implemented in the `pkg/transport/factory.go` file.

Here's an example of how the transport is created internally:

```go
func (*Factory) Create(config types.Config) (types.Transport, error) {
    switch config.Type {
    case types.TransportTypeStdio:
        return NewStdioTransport(config.Port, config.Runtime, config.Debug, config.Middlewares...), nil
    case types.TransportTypeSSE:
        return NewSSETransport(
            config.Host,
            config.Port,
            config.TargetPort,
            config.Runtime,
            config.Debug,
            config.TargetHost,
            config.Middlewares...,
        ), nil
    default:
        return nil, errors.ErrUnsupportedTransport
    }
}
```

## Best Practices

1. Choose the appropriate transport mode based on your specific use case and requirements.
2. For local development and debugging, STDIO transport is often simpler and easier to use.
3. For production environments or scenarios requiring real-time updates, consider using SSE transport.
4. When using SSE transport, ensure that you configure the correct ports and target host to avoid connection issues.
5. Always use the `--debug` flag when troubleshooting transport-related issues to get more detailed logging information.

By understanding and properly configuring the transport modes in ToolHive, you can ensure efficient communication between your client and MCP server for various scenarios and deployment environments.