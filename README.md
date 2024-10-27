# Kubernetes Dashboard Deployment with Helm

This repository contains files to deploy the Kubernetes Dashboard on your cluster using Helm, including setup for external access via Ingress and an admin user service account for secure login.

## Prerequisites

- Kubernetes cluster with `kubectl` configured.
- Helm installed.
- Cert-manager installed. Refer to [this guide](https://medium.com/@aman07mishra/how-to-add-cert-manager-to-your-kubernetes-cluster-574b4837f71d) for installation instructions.

## Steps to Deploy

### 1. Install Kubernetes Dashboard with Helm

Add the Helm chart repository and deploy the Kubernetes Dashboard:

```bash
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace -n kubernetes-dashboard
```

### 2. Configure Ingress for Secure Access

Apply the provided Ingress configuration file to enable external access with SSL.

Refer to [this guide on setting up cert-manager](https://medium.com/@aman07mishra/how-to-add-cert-manager-to-your-kubernetes-cluster-574b4837f71d) for installation instructions.

```bash
kubectl apply -f k8s_ingress.yaml -n kubernetes-dashboard
```

The k8s_ingress.yaml file includes:

	• NGINX Ingress annotations for SSL passthrough.
	• Specified cert-manager annotations to automatically issue SSL certificates.
	• Replace <CLUSTER-ISSUER> and <HOST-NAME> in k8s_ingress.yaml with your actual cluster issuer and domain name.

### 3. Set Up Service Account and ClusterRoleBinding

To access the dashboard, set up a service account with ClusterRoleBinding by applying kdash.yaml:
```bash
kubectl apply -f kdash.yaml --namespace kubernetes-dashboard
```
Retrieve the token for admin access:
```bash
kubectl get secret admin-user --namespace kubernetes-dashboard -o yaml
```
Extract and decode the token with:
```bash
echo "<TOKEN_VALUE>" | base64 --decode
```
Use this token to log in at the Kubernetes Dashboard URL: https://<HOST-NAME>

## Files in This Repository

	• k8s_ingress.yaml - Configures NGINX Ingress for Dashboard access with SSL.
	• kdash.yaml - Sets up the admin-user service account and cluster role binding for admin access.
	• .gitignore - Standard Git ignore file to exclude unnecessary files.

## Troubleshooting

	• Ingress Not Working: Check that Ingress is correctly configured and that cert-manager is issuing certificates.
	• Token Errors: Ensure admin-user secret exists and token is valid. If issues persist, delete and recreate the admin-user.

Happy Dashboarding!
