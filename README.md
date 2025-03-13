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
The git-sync image is typically used in Kubernetes to synchronize a Git repository with a local directory inside a container or a pod. It is a popular choice for scenarios where you want to keep the contents of a Git repository up to date within a Kubernetes cluster.

#### Here's what the git-sync image does:
- Repository Cloning: When a container with the git-sync image is started, it clones a specified Git repository to a local directory inside the container.
- Synchronization: After the initial clone, the git-sync container periodically checks for updates in the Git repository. It can be configured to check for updates at specific intervals.
- Pulling Updates: When updates are detected in the Git repository, the git-sync container pulls these updates into the local directory. This ensures that the contents of the local directory are always synchronized with the latest changes in the Git repository.
- Volume Mounting: Often, the synchronized directory is mounted as a volume to other containers or pods in the same Kubernetes deployment. This allows other containers or applications to access the synchronized Git repository's contents.

#### Overall, git-sync is a useful tool for maintaining consistency and automatically updating data from a Git repository within a Kubernetes environment.

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
## Deploying the Configuration
Apply the configuration files to your Kubernetes cluster using `kubectl apply -f project.yaml`

## Accessing the Web App on Docker Kubernetes
Once deployed, you can access the blog web app by using the port-forward command:
```
kubectl port-forward service/blog-service 8099:80
```
#### The Application would be accessible at: http://localhost:8099

## Accessing the Web App on Minikube with Docker Driver
Once deployed, you can access the blog web app by using port-forward command:
```
minikube service blog-service -p minikube
```
#### After running the above command the application would be opened automatically

# Thank you! 👋

## 🔗 Connect with Me
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/tejas-durge-0a62a128a/)
[![twitter](https://img.shields.io/badge/twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/TejasDurge55)

