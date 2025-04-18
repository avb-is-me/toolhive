# Using the ToolHive Registry System

The ToolHive Registry System provides a centralized repository for managing and retrieving information about various servers. This document explains how to use the registry system, including searching for servers, retrieving server information, and understanding the structure of server entries.

## Table of Contents

1. [Overview](#overview)
2. [Retrieving Registry Information](#retrieving-registry-information)
3. [Getting Server Information](#getting-server-information)
4. [Searching for Servers](#searching-for-servers)
5. [Listing All Servers](#listing-all-servers)
6. [Server Entry Structure](#server-entry-structure)

## Overview

The ToolHive Registry System is implemented in the `registry` package. It provides functions to interact with the registry, which is loaded from an embedded JSON file. The registry contains information about various servers, including their names, descriptions, and tags.

## Retrieving Registry Information

To get access to the entire registry, you can use the `GetRegistry()` function:

```go
registry, err := registry.GetRegistry()
if err != nil {
    // Handle error
}
```

This function returns a pointer to the `Registry` struct, which contains all the server information.

## Getting Server Information

To retrieve information about a specific server, use the `GetServer()` function:

```go
serverName := "example-server"
server, err := registry.GetServer(serverName)
if err != nil {
    // Handle error (e.g., server not found)
}

// Use server information
fmt.Printf("Server Name: %s\n", server.Name)
fmt.Printf("Description: %s\n", server.Description)
```

## Searching for Servers

The registry system provides a search functionality to find servers based on a query string. The search looks for matches in server names, descriptions, and tags:

```go
query := "database"
results, err := registry.SearchServers(query)
if err != nil {
    // Handle error
}

for _, server := range results {
    fmt.Printf("Found server: %s\n", server.Name)
}
```

## Listing All Servers

To get a list of all servers in the registry, use the `ListServers()` function:

```go
servers, err := registry.ListServers()
if err != nil {
    // Handle error
}

for _, server := range servers {
    fmt.Printf("Server: %s\n", server.Name)
}
```

## Server Entry Structure

Each server entry in the registry contains the following information:

- `Name`: The name of the server (string)
- `Description`: A brief description of the server (string)
- `Tags`: A list of tags associated with the server ([]string)

Here's an example of how a server entry might look in the registry:

```go
type Server struct {
    Name        string   `json:"name"`
    Description string   `json:"description"`
    Tags        []string `json:"tags"`
}
```

When working with server entries, you can access these fields directly:

```go
server, err := registry.GetServer("example-server")
if err != nil {
    // Handle error
}

fmt.Printf("Server Name: %s\n", server.Name)
fmt.Printf("Description: %s\n", server.Description)
fmt.Printf("Tags: %v\n", server.Tags)
```

By using these functions and understanding the structure of server entries, you can effectively work with the ToolHive Registry System to manage and retrieve server information.