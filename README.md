# MyApp Kubernetes Deployment Project

## Project Overview

This project deploys a complete application stack on Kubernetes using
Helm, consisting of:

-   MySQL database (StatefulSet)
-   Nginx web server (Deployment with HPA)
-   Metrics Server for monitoring
-   Ingress controller for external access

------------------------------------------------------------------------

## Project Structure

    myapp-chart/
    ├── templates/
    │   ├── namespace.yaml           # Namespace for all resources
    │   ├── mysql-secret.yaml        # MySQL credentials
    │   ├── mysql-pv.yaml            # Persistent volume claim for MySQL
    │   ├── mysql-statefulset.yaml   # MySQL statefulset
    │   ├── nginx-deployment.yaml    # Nginx deployment
    │   ├── nginx-service.yaml       # Nginx service
    │   ├── nginx-hpa.yaml           # Horizontal pod autoscaler for Nginx
    │   ├── ingress.yaml             # Ingress rules
    │   └── metrics-server.yaml      # Metrics server for HPA
    └── Chart.yaml                   # Helm chart metadata

------------------------------------------------------------------------

## Prerequisites

-   Minikube installed
-   Helm installed
-   kubectl configured

------------------------------------------------------------------------

## Deployment Steps

### 1. Start Minikube

Start Minikube with the specified image repository:

``` bash
minikube start   --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
```

### 2. Deploy Ingress Controller

``` bash
kubectl apply -f ingress-controller.yaml
```

### 3. Patch Ingress Controller

``` bash
kubectl patch deployment -n ingress-nginx ingress-nginx-controller --patch-file ingress-controller-patch.yaml
```

### 4. Deploy the Application with Helm

``` bash
helm install myapp-release ./myapp-chart
```

------------------------------------------------------------------------

## Access the Application

After deployment, access Nginx at:

https://nginx.local/

If needed, add the domain to your hosts file:

``` bash
echo "$(minikube ip) nginx.local" | sudo tee -a /etc/hosts
```

------------------------------------------------------------------------

## Components

### MySQL StatefulSet

-   Stable network identity
-   Persistent volume for data storage
-   Credentials stored as Kubernetes secrets

### Nginx Deployment

-   Serves content on port 80
-   Configured with Horizontal Pod Autoscaler
-   Scales between 3--10 replicas based on CPU usage

### Metrics Server

-   Provides resource metrics
-   Required for HPA functionality
-   Deployed in kube-system namespace

### Ingress

-   Routes external traffic
-   Configured for nginx.local
-   HTTPS enabled

------------------------------------------------------------------------

## Verification Commands

``` bash
kubectl get pods -n mytask
kubectl get svc -n mytask
kubectl get ingress -n mytask
kubectl get hpa -n mytask
kubectl get pvc -n mytask
```

------------------------------------------------------------------------

## Troubleshooting

``` bash
kubectl get pods -n ingress-nginx
kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx
kubectl get ingressclass
```

------------------------------------------------------------------------

## Clean Up

``` bash
helm uninstall myapp-release
kubectl delete -f ingress-controller.yaml
minikube stop
```

------------------------------------------------------------------------

## Notes

-   All resources deploy into the `mytask` namespace
-   MySQL data persists across restarts
-   Nginx auto-scales based on CPU load
-   Ingress controller patch ensures proper configuration
