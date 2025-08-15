# Kubernetes OIDC Demo

This demo shows how to configure Kubernetes authentication using OpenID Connect (OIDC) with Google as the identity provider. This allows users to authenticate to Kubernetes clusters using their Google accounts instead of managing separate Kubernetes credentials.


<p align="center">
  <a href="https://www.youtube.com/watch?v=2P0EBhnGcW0" target="_blank">
    <img src="https://img.youtube.com/vi/2P0EBhnGcW0/hqdefault.jpg" alt="Voltage Park Kubernetes OIDC – YouTube video thumbnail" width="640" />
  </a>
</p>

## Overview

The demo includes:
- A kubeconfig file configured for OIDC authentication
- A ClusterRoleBinding that grants permissions to authenticated users
- Step-by-step setup instructions

## Prerequisites

- A Kubernetes cluster with OIDC authentication enabled
- `kubectl` installed locally
- The `kubelogin` plugin installed
- A Google Cloud project with OAuth 2.0 credentials configured

## Setup Instructions

### 1. Install kubectl oidc-login plugin

```bash
brew install kubelogin
```

### 2. Configure Google OAuth 2.0

1. Go to the [Google Cloud Auth Console](https://console.cloud.google.com/auth/overview)
2. Create a new project or select an existing one
3. Set the application type to "Desktop application"
4. Note down the Client ID and Client Secret

### 3. Configure the Kubernetes cluster

Your Kubernetes API server needs to be configured with OIDC parameters. Supply an [auth config](/kubernetes/oidc/auth-config.yaml) when creating your cluster.

### 4. Update the configuration files

#### Update kubeconfig.yml

Replace the following placeholders in `kubeconfig.yml`:
- `YOUR-K8S-CA-DATA`: Base64-encoded CA certificate of your Kubernetes cluster
- `YOUR-K8s-API-SERVER`: URL of your Kubernetes API server (e.g., `https://your-cluster.example.com:6443`)
- `YOUR-CLIENT-ID`: Google OAuth 2.0 Client ID
- `YOUR-CLIENT-SECRET`: Google OAuth 2.0 Client Secret

#### Update rb.yaml

Replace the placeholder in `rb.yaml`:
- `YOUR-EMAIL@YOUR-COMPANY.com`: The Google email address that should have access

### 5. Apply the RBAC configuration

Using the cluster admin kubeconfig returned after the cluster was created.

```bash
kubectl apply -f rb.yaml
```

### 6. Use the OIDC kubeconfig

```bash
# Copy the kubeconfig to your kubectl config location
cp kubeconfig-oidc.yml ~/.kube/config

# Or use it with the KUBECONFIG environment variable
export KUBECONFIG=/path/to/kubeconfig-oidc.yml

# Test the authentication
kubectl get pods
```

## How it works

1. When you run a `kubectl` command, the OIDC plugin is triggered
2. It opens a browser window for Google OAuth authentication
3. After successful authentication, Google returns an ID token
4. The ID token is sent to the Kubernetes API server
5. The API server validates the token with Google's OIDC endpoint
6. If valid, the user's email from the token is used for RBAC authorization

## Additional Resources

- [Kubernetes OIDC Documentation](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#openid-connect-tokens)
- [kubectl oidc-login Plugin](https://github.com/int128/kubelogin)
- [Google OAuth 2.0 Documentation](https://developers.google.com/identity/protocols/oauth2)