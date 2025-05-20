# Exercise 1

1. Create the namespace

```sh
kubectl create namespace exercise1
```

2. Create the deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: exercise1-deployment
  namespace: exercise1
  labels:
    app: exercise1-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: exercise1-deployment
  template:
    metadata:
      labels:
        app: exercise1-deployment
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
          - containerPort: 80
```

  
