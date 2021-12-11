# Kubernetes Engine

>> Note: Cluster names must start with a letter and end with an alphanumeric, and cannot be longer than 40 characters.

### To create a cluster, run the following command, replacing [CLUSTER-NAME] with the name you choose for the cluster (for example:my-cluster).

```bash
gcloud container clusters create [CLUSTER-NAME]
```

gcloud container clusters create my-cluster

### Get authentication credentials for the cluster

To authenticate the cluster, run the following command, replacing [CLUSTER-NAME] with the name of your cluster:

```bash
gcloud container clusters get-credentials [CLUSTER-NAME]
```

### Deploy and expose

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

### Deleting the cluster

```bash
gcloud container clusters delete [CLUSTER-NAME]
```