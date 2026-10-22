# Kubernetes Basics

Kubernetes is a tool that allows you to create and manage an environment with a lot of containers.



## Prerequisites

  - [ Homebrew ] = Package Manager
  - [ minikube ] = local Kubernetes 
```bash
brew install minikube
```
  - [ kubectl ] = CLI di Kubernetes
```bash
# included in Docker Desktop, otherwise `brew install kubectl`.
```
  - [ Docker Desktop ] = Containerization software (driver used by minikube)

NOTE: Docker Desktop must run to work with it!



## Commands for starting the environment


### Starting minikube

```bash
minikube start --driver=docker
```


### Creating the Pods, based on images from DockerHub

```bash
kubectl create deployment nginx-depl --image=nginx
kubectl create deployment mongo-depl --image=mongo
```

NOTE: "deployment" is the blueprint for creating Pods.


### Seeing the ReplicaSet created

```bash
kubectl get replicaset
kubectl get pod
```

NOTE: "replicaset" is the controller object that allows to have a certain number of Pod instances always in execution.
The ReplicaSet name is formed as " <deployment_name>-<replicaset_id> ".
The Pod name is formed as " <deployment_name>-<replicaset_id>-<pod_id> ".


### Deleting the Deployments (and their Pods)

```bash
kubectl delete deployment nginx-depl
kubectl delete deployment mongo-depl
```



## Creating the configuration files


### Deployment file (YAML)

```bash
touch nginx-deployment.yaml
vim nginx-deployment.yaml
```

Now you have created and opened a file YAML (Deployment) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: apps/v1
# Type of configuration file
kind: Deployment

# 1. Metadata
metadata:
  name: nginx-deployment
  labels:
    app: nginx

# 2. Specification
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
        image: nginx
        ports:
        - containerPort: 8080

# 3. Status
# Not written by you: Kubernetes generates it in the live object (see "Result file").
```

NOTE: This is the procedure on MacOS, using the VIM Editor.
Now you have a base configuration file (Deployment) to create the nginx Pods (2 replicas).

```bash
kubectl apply -f nginx-deployment.yaml
```


### Service file (YAML)

```bash
touch nginx-service.yaml
vim nginx-service.yaml
```

Now you have created and opened a file YAML (Service) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: v1
# Type of configuration file
kind: Service

# 1. Metadata
metadata:
  name: nginx-service

# 2. Specification
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

# 3. Status
# Not written by you: Kubernetes generates it in the live object (see "Result file").
```

NOTE: This is the procedure on MacOS, using the VIM Editor.
Now you have a base configuration file (Service) to expose the nginx Pods inside the cluster.

```bash
kubectl apply -f nginx-service.yaml
kubectl get deployment,service,pod
kubectl describe service nginx-service
```



### Testing the Service

The Service is of type `ClusterIP`, so it is reachable only inside the cluster.
To test it from your machine, create a temporary tunnel:
```bash
kubectl port-forward service/nginx-service 8081:80
```
NOTE: "8081:80" means "<local_port>:<Service_port>". The command keeps running in the foreground (stop it with Ctrl+C).

In another terminal:
```bash
curl localhost:8081
```
You should see the HTML of the "Welcome to nginx!" page.



### Result file (to see Status)

```bash
kubectl get deployment nginx-deployment -o yaml > nginx-deployment-result.yaml
```



## Deleting Deployment and Service with the configuration file

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```



## Thanks to

Thanks to [TechWorld with Nana](https://www.youtube.com/@techworldwithnana) for the Kubernetes tutorial this project is based on.
Check out her channel and her [website](https://www.techworld-with-nana.com/) for more DevOps and cloud content.
The reference video is ["Kubernetes tutorial for Beginners"](https://www.youtube.com/watch?v=X48VuDVv0do).
