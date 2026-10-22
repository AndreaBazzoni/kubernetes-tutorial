# Kubernetes Tutorial

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



## Creating the configuration files


### Secret configuration file (YAML)

```bash
touch mongodb-secret.yaml
vim mongodb-secret.yaml
```

Now you have created and opened a file YAML (Secret) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: v1
# Type of configuration file
kind: Secret

# 1. Metadata
metadata:
  name: mongodb-secret

# 2. Type
type: Opaque

# 3. Data
data:
  mongo-root-username: <your_username_ENCRYPTED>
  mongo-root-password: <your_password_ENCRYPTED>
```

NOTE: This is the procedure on MacOS, using the VIM Editor.
NOTE: The Secret file is NOT secure. You have to input <username> and <password> ENCRYPTED!
If you want to use (for exercise) the Base64 encryption:
```bash
echo -n "<your_username>" | base64
echo -n "<your_password>" | base64
```

```bash
kubectl apply -f mongodb-secret.yaml
```


### MongoDB configuration file (YAML)

```bash
touch mongodb-deployment.yaml
vim mongodb-deployment.yaml
```

Now you have created and opened a file YAML (MongoDB) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: apps/v1
# Type of configuration file
kind: Deployment

# 1. Metadata
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb

# 2. Specification
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password

# 3. Status
# Not written by you: Kubernetes generates it in the live object (see "Result file").

# File separator
---

apiVersion: v1
# Type of configuration file
kind: Service

# 1. Metadata
metadata:
  name: mongodb-service

# 2. Specification
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017

# 3. Status
# Not written by you: Kubernetes generates it in the live object (see "Result file").
```

NOTE: This is the procedure on MacOS, using the VIM Editor.

```bash
kubectl apply -f mongodb.yaml
```


### ConfigMap configuration file (YAML)

```bash
touch mongodb-configmap.yaml
vim mongodb-configmap.yaml
```

Now you have created and opened a file YAML (ConfigMap) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: v1
# Type of configuration file
kind: ConfigMap

# 1. Metadata
metadata:
  name: mongodb-configmap

# 2. Data
data:
  database_url: mongodb-service
```

NOTE: This is the procedure on MacOS, using the VIM Editor.

```bash
kubectl apply -f mongodb-configmap.yaml
```


### Mongo-express configuration file (YAML)

```bash
touch mongo-express.yaml
vim mongo-express.yaml
```

Now you have created and opened a file YAML (mongo-express) for the configuration.
Inside it, you have to write the following lines:

```yaml
apiVersion: apps/v1
# Type of configuration file
kind: Deployment

# 1. Metadata
metadata:
  name: mongo-express-deployment
  labels:
    app: mongo-express

# 2. Specification
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom:
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url

# 3. Status
# Not written by you: Kubernetes generates it in the live object.
```

NOTE: This is the procedure on MacOS, using the VIM Editor.

```bash
kubectl apply -f mongo-express.yaml
```



## Deleting with the configuration file

```bash
kubectl delete -f mongodb.yaml
kubectl delete -f mongodb-secret.yaml
```



## Thanks to

Thanks to [TechWorld with Nana](https://www.youtube.com/@techworldwithnana) for the Kubernetes tutorial this project is based on.
Check out her channel and her [website](https://www.techworld-with-nana.com/) for more DevOps and cloud content.
The reference video is ["Kubernetes tutorial for Beginners"](https://www.youtube.com/watch?v=X48VuDVv0do).
