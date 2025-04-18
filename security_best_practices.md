# Security Best Practices for ToolHive

This document outlines the security best practices for using ToolHive, including proper handling of secrets, setting up authorization and authentication, and ensuring secure communication between components.

## Table of Contents
1. [Handling Secrets](#handling-secrets)
2. [Authorization and Authentication](#authorization-and-authentication)
3. [Secure Communication](#secure-communication)
4. [Container Security](#container-security)

## Handling Secrets

ToolHive provides a secure way to manage secrets using encryption. Here are the best practices for handling secrets:

### Using the Encrypted Secret Manager

1. Use the `EncryptedType` secret manager for storing sensitive information:

```go
secretManager, err := secrets.CreateSecretManager(secrets.EncryptedType)
if err != nil {
    // Handle error
}
```

2. Set a strong password for encrypting and decrypting secrets:
   - Use the `TOOLHIVE_SECRETS_PASSWORD` environment variable to set the password.
   - If not set, ToolHive will prompt for a password and store it securely in the system keyring.

3. Avoid hardcoding secrets in your code or configuration files.

4. Regularly rotate secrets and update them in the secret manager.

### Best Practices for Password Management

- Use a strong, unique password for the `TOOLHIVE_SECRETS_PASSWORD`.
- Don't share the password or store it in plaintext.
- If you suspect the password has been compromised, use the `ResetKeyringSecret()` function to clear the stored secret and set a new password.

## Authorization and Authentication

ToolHive supports both authentication via OAuth2/OpenID Connect (OIDC) and authorization using Cedar policies.

### Setting up OIDC Authentication

1. Configure OIDC in your runner configuration:

```go
runConfig := &RunConfig{
    OIDCConfig: &auth.OIDCConfig{
        Issuer:   "https://your-oidc-provider.com",
        Audience: "your-audience",
        JWKSURL:  "https://your-oidc-provider.com/.well-known/jwks.json",
        ClientID: "your-client-id",
    },
    // Other configuration options...
}
```

2. The OIDC middleware will automatically validate JWT tokens in incoming requests.

### Implementing Authorization with Cedar Policies

1. Create Cedar policies that define access rules for your resources.

2. Configure the authorization in your runner:

```go
runConfig := &RunConfig{
    AuthzConfig: &authz.Config{
        Policies: []string{
            "permit(principal, action, resource) when { ... }",
            // Add more policies as needed
        },
        EntitiesJSON: `{
            "entities": [
                // Define your entities here
            ]
        }`,
    },
    // Other configuration options...
}
```

3. The authorization middleware will enforce these policies for each request.

### Best Practices for Authorization

- Follow the principle of least privilege when defining Cedar policies.
- Regularly review and update your policies to ensure they align with your security requirements.
- Use specific and granular permissions rather than broad, all-encompassing rules.

## Secure Communication

Ensure secure communication between ToolHive components and external services:

1. Use HTTPS for all external communications.
2. Validate SSL/TLS certificates for outgoing connections.
3. Implement proper error handling to avoid exposing sensitive information in error messages.

## Container Security

When using ToolHive with containerized environments:

1. Use the `PermissionProfile` option in the runner configuration to set appropriate container permissions:

```go
runConfig := &RunConfig{
    PermissionProfile: "restricted",
    // Other configuration options...
}
```

2. Regularly update the container images used by ToolHive to include the latest security patches.
3. Follow the principle of least privilege when configuring container access and capabilities.

By following these security best practices, you can help ensure that your ToolHive implementation remains secure and robust against potential threats.