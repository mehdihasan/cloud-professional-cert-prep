# Getting Started with Google Kubernetes Engine 



export my_zone=us-central1-a
export my_cluster=standard-cluster-1
source <(kubectl completion bash)
gcloud container clusters get-credentials $my_cluster --zone $my_zone
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/Deployments/

nginx-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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
        image: nginx:1.7.9
        ports:
        - containerPort: 80

```
## Task 2. Manually scale up and down the number of Pods in deployments
kubectl apply -f ./nginx-deployment.yaml
kubectl get deployments
kubectl scale --replicas=1 deployment nginx-deployment
kubectl get deployments
kubectl scale --replicas=3 deployment nginx-deployment
## Task 3. Trigger a deployment rollout and a deployment rollback
### Trigger a deployment rollout
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1 --record

kubectl rollout status deployment.v1.apps/nginx-deployment

kubectl rollout history deployment nginx-deployment

### Trigger a deployment rollback
kubectl rollout undo deployments nginx-deployment
kubectl rollout history deployment nginx-deployment
kubectl rollout history deployment/nginx-deployment --revision=3

## Task 4. Define the service type in the manifest
service-nginx.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 60000
    targetPort: 80

```
kubectl apply -f ./service-nginx.yaml
kubectl get service nginx

## Task 5. Perform a canary deployment

nginx-canary.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-canary
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        track: canary
        Version: 1.9.1
    spec:
      containers:
      - name: nginx
        image: nginx:1.9.1
        ports:
        - containerPort: 80

```
kubectl apply -f nginx-canary.yaml

kubectl get deployments

kubectl scale --replicas=0 deployment nginx-deployment

### Session affinity

sessionAffinity .yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  sessionAffinity: ClientIP
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 60000
    targetPort: 80

```

## Introduction to Google Cloud

## Introduction to Containers and Kubernetes

## Kubernetes Architecture

## Introduction to Kubernetes Workloads

## Course Resources