# 🚀 Auto Fetch Git Repo with help of PersistentVolumeClaim in Kubernetes

This project demonstrates how to create a PersistentVolumeClaim and deploy applications on Kubernetes using YAML configuration files.

## Prerequisites
- Kubernetes cluster up and running.
- `kubectl` command-line tool installed and configured.

#### 🛠 Skills Required:
- Kubernetes Basics

## Creating a PersistentVolumeClaim

### Step 1: Create the PersistentVolumeClaim
You can create a PersistentVolumeClaim for your applications using the following YAML configuration file:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: html
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 0.5Gi  # Adjust the size as needed
```
## Deploying Nginx and Git-Sync Deployments
### Step 2: Deploy Nginx
You can deploy Nginx with 5 replicas using the following YAML configuration file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blog
spec:
  replicas: 5  # 5 replicas of Nginx
  selector:
    matchLabels:
      name: blog
  template:
    metadata:
      labels:
        name: blog
    spec:
      volumes:  # Define the volume here
      - name: html
        persistentVolumeClaim:
          claimName: html
      containers:
      - name: nginx
        image: nginx
        ports:
          - containerPort: 80
        volumeMounts:
        - name: html
          mountPath: /usr/share/nginx/

```

### Step 3: Deploy Git-Sync
You can deploy Git-Sync with a single replica using the following YAML configuration file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: git-sync
spec:
  replicas: 1  # Single replica of Git-Sync
  selector:
    matchLabels:
      name: git-sync
  template:
    metadata:
      labels:
        name: git-sync
    spec:
      volumes:  # Define the volume here
      - name: html
        persistentVolumeClaim:
          claimName: html
      containers:
      - name: git-sync
        image: k8s.gcr.io/git-sync:v3.0.1
        volumeMounts:
        - name: html
          mountPath: /tmp/git
        env:
        - name: GIT_SYNC_REPO
          value: https://github.com/tejasdurge55/dice_game.git
        - name: GIT_SYNC_DEST
          value: html

```
## Creating a Service
### Step 4: Create a Service
You can create a Service for your blog application using the following YAML configuration file:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: blog-service
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 32000
  selector:
    name: blog  # Selector for pods

```

## Accessing the Blog Web App
Once deployed, you can access the blog web app at the NodePort address:
http://localhost:32000
