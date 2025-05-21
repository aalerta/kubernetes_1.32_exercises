# Exercise 1

1. Start minikube 

```sh
minikube start
```

2. Create the namespace

```sh
kubectl create namespace exercise1
```

3. Change the default namespace to exercise1

```sh
kubectl config set-context --current --namespace=exercise1
```

4. Create the deployment and save it to the file ```exercise1_deployment.yaml```

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

Apply the deployment

```sh
kubectl apply -f exercise1_deployment.yaml
```

5. Check if the deployment is running

```sh
kubectl -n exercise1 get all -o wide
```

6. Create the service and save it to the file ```exercise1_service.yaml```

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

Apply the service

```sh
kubectl -n exercise1 apply -f exercise1_service.yaml
```

7. Check if the service is working

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

8. Create a persistent volume in minikube and save it to the file ```exercise1_persistentvolume.yaml```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: exercise1-pv001
spec:
  storageClassName: localpv
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 5Gi
  hostPath:
    path: /data/
```

Apply the persistent volume

```sh
kubectl apply -f exercise1_persistentvolume.yaml
```

9. Create a persistent volume claim and save it to the file ```exercise1_persistentvolumeclaim.yaml```

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: exercise1-pvc001
  namespace: exercise1
spec:
  storageClassName: localpv
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply the persistent volume claim

```sh
kubectl apply -f exercise1_persistentvolumeclaim.yaml
```

10. Modify the deployment in order to nginx use the volume instead of default html docroot (and its default html page)

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
          volumeMounts:
            - mountPath: /usr/share/nginx/html
              name: exercise1-volume1
      volumes:
        - name: exercise1-volume1
          persistentVolumeClaim:
            claimName: exercise1-pvc001              
```

Apply the modified deployment

```sh
kubectl apply -f exercise1_deployment.yaml
```

11. Create a shared index.html on the shared volume.

Check the url ```http://192.168.49.2:31080``` in a browser.

Now should give an error because th docroot folder is empty and there is not an index.html.

Access nginx container

```sh
minikube ssh
```

Access nginx container

```sh
minikube ssh
```

Create a new index.html

```sh
sudo bash -c "echo '<html><body><h1>This is exercise 1</h1></body></html>' > /data/index.html"
```

Check the url ```http://192.168.49.2:31080``` in a browser.

Now should be visible the new index.html created.

Leave the nginx container

```sh
exit
```

12. Delete the ```exercise1``` namepace

```sh
kubectl delete namespace exercise1
```

13. Stop minikube

```sh
minikube stop
```

