# Container Runtime Options in ToolHive

ToolHive supports multiple container runtime options, allowing you to choose the best solution for your environment and use case. This document explains the different container runtime options supported by ToolHive, how to configure them, and the pros and cons of each.

## Supported Container Runtimes

ToolHive currently supports the following container runtimes:

1. Docker
2. Podman
3. Kubernetes

## Configuring ToolHive's Container Runtime

ToolHive automatically detects and selects the appropriate container runtime based on the environment. The selection process follows this order of preference:

1. Podman
2. Docker
3. Kubernetes

You don't need to explicitly configure the runtime in most cases. However, if you want to force a specific runtime, you can do so by setting the appropriate environment variables or configuration options (refer to the ToolHive configuration documentation for more details).

## Docker

Docker is a widely-used container runtime that provides a simple and efficient way to build, ship, and run containers.

### Pros:
- Widely adopted and well-documented
- Large ecosystem of tools and images
- Easy to use and set up

### Cons:
- Requires root privileges (daemon runs as root)
- Can be less secure in multi-tenant environments

### When to use:
- Development environments
- Single-user systems
- When you need access to a large variety of pre-built images

### Configuration:
ToolHive will automatically use Docker if it's available and Podman is not present. The Docker socket is typically located at `/var/run/docker.sock`.

## Podman

Podman is a daemonless container engine that provides a Docker-compatible CLI. It can run containers without root privileges, making it a more secure alternative to Docker.

### Pros:
- Daemonless architecture
- Can run without root privileges
- OCI-compliant
- Docker-compatible CLI

### Cons:
- Less widely adopted than Docker
- Smaller ecosystem of tools and images

### When to use:
- Production environments where security is a primary concern
- Systems where running a daemon is not desirable
- When you need rootless container operations

### Configuration:
ToolHive will prefer Podman if it's available. The Podman socket is typically located at `/var/run/podman/podman.sock` or in the XDG runtime directory.

## Kubernetes

Kubernetes is a container orchestration platform that can manage containerized applications across multiple hosts.

### Pros:
- Scalable and robust
- Provides advanced orchestration features
- Ideal for microservices architectures

### Cons:
- More complex to set up and manage
- Steeper learning curve
- May be overkill for simple applications

### When to use:
- Large-scale production environments
- When you need advanced orchestration features
- For managing distributed applications across multiple hosts

### Configuration:
ToolHive will use the Kubernetes runtime if it detects that it's running inside a Kubernetes cluster. This is typically determined by the presence of the `KUBERNETES_SERVICE_HOST` environment variable.

## Code Example: Runtime Detection

Here's a simplified version of how ToolHive detects and selects the appropriate runtime:

```go
func NewFactory() *Factory {
    return &Factory{}
}

func (*Factory) Create(ctx context.Context) (runtime.Runtime, error) {
    if !isKubernetesRuntime() {
        client, err := docker.NewClient(ctx)
        if err != nil {
            return nil, err
        }
        return client, nil
    }

    client, err := kubernetes.NewClient(ctx)
    if err != nil {
        return nil, err
    }

    return client, nil
}

func isKubernetesRuntime() bool {
    return os.Getenv("KUBERNETES_SERVICE_HOST") != ""
}
```

This code snippet demonstrates how ToolHive checks for the Kubernetes environment first, and if not found, defaults to creating a Docker client (which internally checks for Podman before Docker).

## Conclusion

Choosing the right container runtime depends on your specific use case, security requirements, and the scale of your application. ToolHive's flexible runtime support allows you to use the most appropriate solution for your needs while providing a consistent interface for container operations across different runtimes.