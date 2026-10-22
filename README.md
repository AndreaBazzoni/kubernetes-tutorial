# Kubernetes Tutorial

Kubernetes is a tool that allows you to create and manage an environment with a lot of containers.



## Prerequisites

 - [ Homebrew ] = Package Manager
 - [ Docker Desktop ] = Containerization software (and local environment for Kubernetes)

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


### Deleting the Pods

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

Now you have created and opened a file YAML (Destination) for the configuration.
Inside it, you have to write the following rows:

```bash
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
# Automatically generated and added by Kubernetes!
```

NOTE: This is the procedure on MacOS, using the VIM Editor.
Now you have a base configuration file (Deployment) to create the nginx Pod.

```bash
kubectl apply -f nginx-deployment.yaml
```


### Service file (YAML)

```bash
touch nginx-service.yaml
vim nginx-service.yaml
```

Now you have created and opened a file YAML (Service) for the configuration.
Inside it, you have to write the following rows:

```bash
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
# Automatically generated and added by Kubernetes!
```

NOTE: This is the procedure on MacOS, using the VIM Editor.
Now you have a base configuration file (Service) to create the nginx Pod.

```bash
kubectl apply -f nginx-service.yaml
```


