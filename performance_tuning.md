---
title: Performance Tuning
description: Guide for optimizing ToolHive performance and scaling for larger deployments
---

# Performance Tuning

This guide provides recommendations for optimizing ToolHive performance, including efficient use of container resources, minimizing network overhead, and scaling ToolHive for larger deployments.

## Table of Contents

1. [Container Resource Optimization](#container-resource-optimization)
2. [Network Overhead Reduction](#network-overhead-reduction)
3. [Scaling ToolHive](#scaling-toolhive)
4. [Kubernetes-specific Optimizations](#kubernetes-specific-optimizations)

## Container Resource Optimization

### Resource Limits and Requests

When creating containers, it's important to set appropriate resource limits and requests. This helps Kubernetes schedule pods efficiently and prevents resource contention.

```yaml
resources:
  limits:
    cpu: "1"
    memory: "1Gi"
  requests:
    cpu: "500m"
    memory: "512Mi"
```

### Use Lightweight Base Images

Choose lightweight base images for your containers to reduce image size and improve startup times. Alpine-based images are often a good choice for minimizing container footprint.

### Optimize Dockerfile

- Use multi-stage builds to keep final images small
- Combine RUN commands to reduce layers
- Clean up unnecessary files in the same layer they're created

Example:

```dockerfile
FROM golang:1.16-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

FROM alpine:3.14
COPY --from=builder /app/main /main
CMD ["/main"]
```

## Network Overhead Reduction

### Use Headless Services Wisely

ToolHive uses headless services for SSE transport. While these are useful for direct pod-to-pod communication, be mindful of their impact on DNS resolution times in large clusters.

### Optimize Service Discovery

If you're not using Kubernetes' built-in service discovery, consider implementing a service mesh like Istio for more efficient service-to-service communication and load balancing.

### Minimize Cross-Node Traffic

When possible, use node affinity and pod affinity rules to keep related pods on the same node, reducing network overhead:

```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values:
          - toolhive
      topologyKey: "kubernetes.io/hostname"
```

## Scaling ToolHive

### Horizontal Pod Autoscaling

Implement Horizontal Pod Autoscaling (HPA) to automatically adjust the number of pods based on CPU or custom metrics:

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: toolhive-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: toolhive
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

### Vertical Pod Autoscaling

Consider using Vertical Pod Autoscaling to automatically adjust CPU and memory requests based on actual usage patterns.

### Use StatefulSets for Stateful Workloads

ToolHive uses StatefulSets for managing stateful applications. Ensure you're using the appropriate storage class and volume claims for optimal performance:

```yaml
volumeClaimTemplates:
- metadata:
    name: data
  spec:
    accessModes: ["ReadWriteOnce"]
    storageClassName: "fast-storage"
    resources:
      requests:
        storage: 1Gi
```

## Kubernetes-specific Optimizations

### Efficient Service Configuration

When creating services for SSE transport, ToolHive automatically determines the appropriate service type. To optimize performance, consider the following:

- Use ClusterIP services for internal communication
- Use NodePort or LoadBalancer services only when external access is required

### Optimize StatefulSet Configurations

ToolHive creates StatefulSets for managing containers. To improve performance:

- Set an appropriate `updateStrategy` for rolling updates
- Use `podManagementPolicy: Parallel` for faster scaling when order isn't important

Example:

```yaml
spec:
  updateStrategy:
    type: RollingUpdate
  podManagementPolicy: Parallel
```

### Leverage Node Labels and Taints

Use node labels and taints to ensure ToolHive components are scheduled on appropriate nodes:

```yaml
nodeSelector:
  toolhive: "true"

tolerations:
- key: "toolhive"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"
```

By following these performance tuning guidelines, you can optimize ToolHive's resource utilization, reduce network overhead, and effectively scale your deployment to handle larger workloads.