# Configuring Persistent Storage for Google Kubernetes Engine 

## Init

gcloud auth list

gcloud config list project


## Task 1. Create PVs and PVCs

### Connect to the lab GKE cluster

export my_zone=us-central1-a
export my_cluster=standard-cluster-1
source <(kubectl completion bash)
gcloud container clusters get-credentials $my_cluster --zone $my_zone


### Create and apply a manifest with a PVC
pvc-demo.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: hello-web-disk
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Gi

```

git clone https://github.com/GoogleCloudPlatform/training-data-analyst
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
cd ~/ak8s/Storage/
kubectl get persistentvolumeclaim
kubectl apply -f pvc-demo.yaml
kubectl get persistentvolumeclaim


## Task 2. Mount and verify Google Cloud persistent disk PVCs in Pods

### Mount the PVC to a Pod
pod-volume-demo.yaml
```yaml
kind: Pod
apiVersion: v1
metadata:
  name: pvc-demo-pod
spec:
  containers:
    - name: frontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: pvc-demo-volume
  volumes:
    - name: pvc-demo-volume
      persistentVolumeClaim:
        claimName: hello-web-disk
```

kubectl apply -f pod-volume-demo.yaml
kubectl get pods

kubectl exec -it pvc-demo-pod -- sh
echo Test webpage in a persistent volume!>/var/www/html/index.html
chmod +x /var/www/html/index.html
cat /var/www/html/index.html
exit


### Test the persistence of the PV

kubectl delete pod pvc-demo-pod
kubectl get pods
kubectl get persistentvolumeclaim
kubectl apply -f pod-volume-demo.yaml
kubectl get pods
kubectl exec -it pvc-demo-pod -- sh
cat /var/www/html/index.html
exit


## Task 3. Create StatefulSets with PVCs

### Release the PVC

kubectl delete pod pvc-demo-pod
kubectl get pods

statefulset-demo.yaml
```yaml
kind: Service
apiVersion: v1
metadata:
  name: statefulset-demo-service
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
  type: LoadBalancer
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: statefulset-demo
spec:
  selector:
    matchLabels:
      app: MyApp
  serviceName: statefulset-demo-service
  replicas: 3
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: MyApp
    spec:
      containers:
      - name: stateful-set-container
        image: nginx
        ports:
        - containerPort: 80
          name: http
        volumeMounts:
        - name: hello-web-disk
          mountPath: "/var/www/html"
  volumeClaimTemplates:
  - metadata:
      name: hello-web-disk
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 30Gi

```

kubectl apply -f statefulset-demo.yaml
kubectl describe statefulset statefulset-demo
kubectl get pods
kubectl get pvc
kubectl describe pvc hello-web-disk-statefulset-demo-0


## Task 4. Verify the persistence of Persistent Volume connections to Pods managed by StatefulSets

kubectl exec -it statefulset-demo-0 -- sh
cat /var/www/html/index.html
echo Test webpage in a persistent volume!>/var/www/html/index.html
chmod +x /var/www/html/index.html
cat /var/www/html/index.html
exit

kubectl delete pod statefulset-demo-0

kubectl get pods
kubectl exec -it statefulset-demo-0 -- sh
cat /var/www/html/index.html
exit