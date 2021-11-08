# Continuous Delivery with Jenkins in Kubernetes Engine


## Init

gcloud auth list

gcloud config list project

gcloud config set compute/zone us-east1-d

git clone https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes.git

cd continuous-deployment-on-kubernetes


## Provisioning Jenkins

### Creating a Kubernetes cluster

gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--machine-type n1-standard-2 \
--scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"

gcloud container clusters list

gcloud container clusters get-credentials jenkins-cd

kubectl cluster-info


## Setup Helm

helm repo add jenkins https://charts.jenkins.io

helm repo update


## Configure and Install Jenkins

gsutil cp gs://spls/gsp330/values.yaml jenkins/values.yaml

helm install cd jenkins/jenkins -f jenkins/values.yaml --wait

kubectl get pods

kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

kubectl get svc


## Connect to Jenkins

printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

