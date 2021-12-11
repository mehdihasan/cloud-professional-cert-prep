# Kubernetes Engine: Qwik Start

gcloud config set project \
  $(gcloud projects list --format='value(PROJECT_ID)' \
  --filter='qwiklabs-gcp')

gcloud auth list

gcloud config list project

gcloud config set compute/zone us-central1-a

CLUSTER_NAME=my-cluster

## Task 2: Create a GKE cluster

gcloud container clusters create $CLUSTER_NAME


## Task 3: Get authentication credentials for the cluster

gcloud container clusters get-credentials $CLUSTER_NAME


## Task 4: Deploy an application to the cluster

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

kubectl expose deployment hello-server --type=LoadBalancer --port 8080

kubectl get service


## Task 5: Deleting the cluster

gcloud container clusters delete $CLUSTER_NAME