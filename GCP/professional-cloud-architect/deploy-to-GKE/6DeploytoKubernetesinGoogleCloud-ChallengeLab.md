# Deploy to Kubernetes in Google Cloud: Challenge Lab

gcloud auth list

gcloud config list project

gcloud config set project \
  $(gcloud projects list --format='value(PROJECT_ID)' \
  --filter='qwiklabs-gcp')

gcloud config set compute/zone us-east1-d


## Task 1: Create a Docker image and store the Dockerfile

gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh


git clone valkyrie-app

cd valkyrie-app

nano Dockerfile

```
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
```

docker build -t valkyrie-app:v0.0.1 .

step1.sh


## Task 2: Test the created Docker image

docker run -p 8080:8080 --name valkyrie-app valkyrie-app:v0.0.1 &

step2.sh


## Task 3: Push the Docker image in the Container Repository

gcloud config list project

docker tag valkyrie-app:v0.0.1 gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1

docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1


## Task 4: Create and expose a deployment in Kubernetes

gcloud container clusters get-credentials valkyrie-dev

kubectl cluster-info

cd ~/valkyrie-app/k8s

cat deployment.yaml

cat service.yaml

kubectl apply -f deployment.yaml

kubectl apply -f service.yaml


## Task 5: Update the deployment with a new version of valkyrie-app

kubectl scale deployment valkyrie-dev --replicas=3

git merge origin/kurt-dev

docker build -t valkyrie-app:v0.0.2 .

docker tag valkyrie-app:v0.0.2 gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2

docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2

kubectl edit deploy ...


## Task 6: Create a pipeline in Jenkins to deploy your app

printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &


Kyemv1pdYdk8LkvqzEKovk