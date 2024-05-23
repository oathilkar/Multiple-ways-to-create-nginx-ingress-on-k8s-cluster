# Multiple-ways-to-create-nginx-ingress-on-k8s-cluster

Creating an Nginx Ingress on a Kubernetes cluster can be done in various ways, depending on your environment, tools, and requirements. 
Below are different methods to create and manage an Nginx Ingress:

Method 1. Using `kubectl` with YAML Files
This is the most direct method, where you define the Nginx Ingress resources in YAML files and apply them using `kubectl`.

1. **Create a Deployment and Service for Nginx:**

```yaml
# nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

```yaml
# nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

Apply the Deployment and Service:

```sh
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
```

2. **Create an Ingress Resource:**

```yaml
# nginx-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: your-domain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

Apply the Ingress resource:

```sh
kubectl apply -f nginx-ingress.yaml
```

Method 2. Using Helm
Helm is a package manager for Kubernetes, which makes it easy to deploy complex applications.

1. **Install Helm (if not already installed):**

Follow the instructions on the [official Helm website](https://helm.sh/docs/intro/install/).

2. **Add the Nginx Ingress Helm repository and install:**

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install nginx-ingress ingress-nginx/ingress-nginx
```

This will install the Nginx Ingress Controller. You can customize the installation by passing values.

3. **Create an Ingress Resource:**

```yaml
# nginx-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: your-domain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

Apply the Ingress resource:

```sh
kubectl apply -f nginx-ingress.yaml
```

Method 3. Using Kustomize
Kustomize is a tool for customizing Kubernetes YAML configurations.

1. **Create a Kustomization File:**

```yaml
# kustomization.yaml
resources:
- nginx-deployment.yaml
- nginx-service.yaml
- nginx-ingress.yaml
```

2. **Apply the configuration:**

```sh
kubectl apply -k .
```

Method 4. Using a Custom Script
You can automate the process using a custom script, which might be useful for CI/CD pipelines.

1. **Create a Bash Script:**

```sh
#!/bin/bash

# Create Deployment
kubectl apply -f nginx-deployment.yaml

# Create Service
kubectl apply -f nginx-service.yaml

# Create Ingress
kubectl apply -f nginx-ingress.yaml
```

2. **Run the Script:**

```sh
chmod +x create-ingress.sh
./create-ingress.sh
```

Method 5. Using a Kubernetes Operator
Operators are a way to extend the Kubernetes API. An Nginx Ingress Operator can manage the lifecycle of your Ingress resources.

1. **Find and Install an Operator:**

Search for an Nginx Ingress Operator on [OperatorHub.io](https://operatorhub.io/).

2. **Install the Operator:**

Follow the installation instructions provided on OperatorHub.

3. **Create an Ingress Resource:**

```yaml
# nginx-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: your-domain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

Apply the Ingress resource:

```sh
kubectl apply -f nginx-ingress.yaml
```

### Conclusion
Each method has its own use case and advantages. For simple setups, using `kubectl` with YAML files or Helm might be sufficient. For more complex environments, especially those requiring automated and repeatable deployments, using Helm, Kustomize, or Operators would be more appropriate. Choose the method that best fits your workflow and infrastructure requirements.
