# Exercise 1

1. Start minikube 

```sh
minikube start
```

2. Create the namespace

```sh
kubectl create namespace exercise1
```

3. Create the deployment and save it to the file ```exercise1_deployment.yaml```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: exercise1-deployment
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

3. Apply the deployment

```sh
kubectl -n exercise1 apply -f exercise1_deployment.yaml
```

4. Create the service and save it to the file ```exercise1_service.yaml```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: exercise1-service
spec:
  selector:
    app: exercise1-deployment
  type: NodePort
  ports:
  - protocol: TCP
    targetPort: 80
    port: 8080
    nodePort: 31080
```

5. Apply the service

```sh
kubectl -n exercise1 apply -f exercise1_service.yaml
```

6. Check if the service is working

Check the exported services in minikube

```sh
minikube service list
```

Output:

```sh
|-------------|-------------------|--------------|---------------------------|
|  NAMESPACE  |       NAME        | TARGET PORT  |            URL            |
|-------------|-------------------|--------------|---------------------------|
| default     | kubernetes        | No node port |                           |
| exercise1   | exercise1-service |         8080 | http://192.168.49.2:31080 |
| kube-system | kube-dns          | No node port |                           |
|-------------|-------------------|--------------|---------------------------|
```

Check the url ```http://192.168.49.2:31080``` in a browser.

7. Delete the ```exercise1``` namepace

```sh
kubectl delete namespace exercise1
```

8. Stop minikube

```sh
minikube stop
```

