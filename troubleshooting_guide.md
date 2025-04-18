# Troubleshooting Guide

This guide provides solutions for common issues you might encounter when using ToolHive. Follow the steps below to diagnose problems, interpret error messages, and resolve issues.

## Table of Contents

1. [Container Runtime Issues](#container-runtime-issues)
2. [Image Pull Failures](#image-pull-failures)
3. [Permission Errors](#permission-errors)
4. [Network Connectivity Problems](#network-connectivity-problems)
5. [Container Creation and Start Failures](#container-creation-and-start-failures)
6. [Logging and Debugging](#logging-and-debugging)

## Container Runtime Issues

### Problem: Container runtime not found

If you encounter an error message indicating that the container runtime is not found, follow these steps:

1. Ensure that Docker or Podman is installed on your system.
2. Check if the Docker or Podman daemon is running:
   ```
   # For Docker
   sudo systemctl status docker

   # For Podman
   systemctl status podman
   ```
3. Verify that your user has the necessary permissions to access the container runtime.

### Problem: Unable to connect to container runtime

If ToolHive cannot connect to the container runtime, try the following:

1. Check the socket path:
   - Docker: `/var/run/docker.sock`
   - Podman: `/var/run/podman/podman.sock` or `$XDG_RUNTIME_DIR/podman/podman.sock`
2. Ensure the socket file exists and has the correct permissions.
3. If using Podman, make sure the Podman service is running in socket activation mode.

## Image Pull Failures

### Problem: Failed to pull image

If you're unable to pull an image, try these steps:

1. Verify your internet connection.
2. Check if you have the necessary permissions to pull the image from the registry.
3. Ensure that the image name and tag are correct.
4. If using a private registry, verify your authentication credentials.

Example error message:
```
Error: failed to pull image: <image_name>: unauthorized: authentication required
```

Resolution:
```bash
docker login <registry_url>
# or
podman login <registry_url>
```

## Permission Errors

### Problem: Permission denied when accessing files or directories

If you encounter permission errors when ToolHive tries to access files or directories, follow these steps:

1. Check the permissions of the files or directories in question:
   ```bash
   ls -l <path_to_file_or_directory>
   ```
2. Ensure that the user running ToolHive has the necessary read/write permissions.
3. If using volume mounts, verify that the correct permissions are set in the `permissions.Profile`:

```go
profile := &permissions.Profile{
    Read: []permissions.MountDeclaration{"/path/to/read/only/dir:/container/path:ro"},
    Write: []permissions.MountDeclaration{"/path/to/read/write/dir:/container/path"},
}
```

## Network Connectivity Problems

### Problem: Container cannot access the network

If your container is unable to access the network, try the following:

1. Check if the container is running with the correct network mode:
   ```bash
   docker inspect <container_id> | grep NetworkMode
   # or
   podman inspect <container_id> | grep NetworkMode
   ```
2. Ensure that the `permissions.Profile` allows network access:

```go
profile := &permissions.Profile{
    Network: &permissions.NetworkPermissions{
        Outbound: &permissions.OutboundNetworkPermissions{
            InsecureAllowAll: true,
        },
    },
}
```

3. Verify that your firewall settings are not blocking the container's network access.

## Container Creation and Start Failures

### Problem: Failed to create or start container

If you're unable to create or start a container, follow these steps:

1. Check the error message for specific details about the failure.
2. Verify that the container name is unique and not already in use.
3. Ensure that the required ports are not already in use by other processes.
4. Check if you have sufficient system resources (CPU, memory, disk space) to create and run the container.

Example error and resolution:

Error:
```
Error: failed to create container: Conflict. The container name "/my-container" is already in use.
```

Resolution:
```bash
docker rm my-container
# or
podman rm my-container
```

Then try creating the container again.

## Logging and Debugging

To troubleshoot issues more effectively, you can enable debug mode and check the logs:

1. Run ToolHive with the debug flag:
   ```bash
   thv run --debug <your_command>
   ```

2. Check the logs for detailed information about the operations being performed.

3. If you need to inspect a running container, you can use the following commands:

   ```bash
   # View container logs
   docker logs <container_id>
   # or
   podman logs <container_id>

   # Execute a command inside the container
   docker exec -it <container_id> /bin/sh
   # or
   podman exec -it <container_id> /bin/sh
   ```

4. To get more information about a container's configuration and state:

   ```bash
   docker inspect <container_id>
   # or
   podman inspect <container_id>
   ```

By following this troubleshooting guide, you should be able to diagnose and resolve most common issues encountered when using ToolHive. If you continue to experience problems, please consult the ToolHive documentation or reach out to the support team for further assistance.