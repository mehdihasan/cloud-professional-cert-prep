#  Serverless Cloud Run Development: Challenge Lab 

## BUILD_PROJECT

go build -o server

OR

npm install



## Provision the Qwiklabs environment

gcloud config set project \
  $(gcloud projects list --format='value(PROJECT_ID)' \
  --filter='qwiklabs-gcp')

gcloud config set run/region us-central1

gcloud config set run/platform managed

git clone https://github.com/Deleplace/pet-theory.git


## Task 1: Enable a Public Service: Billing Service


git clone https://github.com/rosera/pet-theory.git && cd pet-theory/lab07

npm-install



gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/billing-staging-api:0.1


gcloud run deploy public-billing-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/billing-staging-api:0.1 \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --max-instances=1

https://public-billing-service-timgvfhwwq-uc.a.run.app

## Task 2: Deploy a Frontend Service

cd ~/pet-theory/lab07/staging-frontend-billing

npm-install

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1


gcloud run deploy frontend-staging-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1 \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --max-instances=1

https://frontend-staging-service-timgvfhwwq-uc.a.run.app

## Task 3: Deploy a Private Service

BILLING_SERVICE=private-billing-service

cd ~/pet-theory/lab07/staging-api-billing

npm install

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/billing-staging-api:0.2


gcloud run deploy $BILLING_SERVICE \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/billing-staging-api:0.2 \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1


BILLING_URL=$(gcloud run services describe $BILLING_SERVICE \
  --platform managed \
  --region us-central1 \
  --format "value(status.url)")


curl -X get -H "Authorization: Bearer $(gcloud auth print-identity-token)" $BILLING_URL


URL: https://private-billing-service-timgvfhwwq-uc.a.run.app

## Task 4: Create a Billing Service Account

gcloud iam service-accounts create billing-service-sa --display-name "Billing Service Cloud Run"


///////////////////////
gcloud run services add-iam-policy-binding billing-service \
  --member=serviceAccount:billing-service-sa@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com \
  --role=roles/run.invoker \
  --region us-central1 \
  --platform managed

PROJECT_NUMBER=$(gcloud projects list \
 --format="value(PROJECT_NUMBER)" \
 --filter="$GOOGLE_CLOUD_PROJECT")

gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member=serviceAccount:service-$PROJECT_NUMBER@gcp-sa-pubsub.iam.gserviceaccount.com \


## Task 5: Deploy the Billing Service

PROD_BILLING_SERVICE=billing-prod-service

cd ~/pet-theory/lab07/prod-api-billing

npm install

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/billing-prod-api:0.1


gcloud run deploy $PROD_BILLING_SERVICE \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/billing-prod-api:0.1 \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1


gcloud run services add-iam-policy-binding $PROD_BILLING_SERVICE --member=serviceAccount:billing-service-sa@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-central1 --platform managed


PROD_BILLING_URL=$(gcloud run services \
  describe $PROD_BILLING_SERVICE \
  --platform managed \
  --region us-central1 \
  --format "value(status.url)")


echo $PROD_BILLING_URL


curl -X get -H "Authorization: Bearer \
  $(gcloud auth print-identity-token)" \
  $PROD_BILLING_URL


https://billing-prod-service-timgvfhwwq-uc.a.run.app

## Task 6: Frontend Service Account


gcloud iam service-accounts create frontend-service-sa --display-name "Billing Service Cloud Run Invoker"

gcloud run services add-iam-policy-binding frontend-prod-service \
  --member=serviceAccount:frontend-service-sa@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com \
  --role=roles/run.invoker \
  --region us-central1 \
  --platform managed

PROJECT_NUMBER=$(gcloud projects list \
 --format="value(PROJECT_NUMBER)" \
 --filter="$GOOGLE_CLOUD_PROJECT")

gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member=serviceAccount:frontend-service-sa@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com \
  --role=roles/iam.serviceAccountTokenCreator


## Task 5: Redeploy the Frontend Service


cd ~/pet-theory/lab07/prod-frontend-billing

BUILD-PROJECT

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-prod:0.1


gcloud run deploy frontend-prod-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-prod:0.1 \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1


gcloud run services add-iam-policy-binding frontend-prod-service --member=serviceAccount:frontend-service-sa@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-central1 --platform managed


FE_PROD_URL=$(gcloud run services \
  describe frontend-prod-service \
  --platform managed \
  --region us-central1 \
  --format "value(status.url)")

curl -X get -H "Authorization: Bearer \
  $(gcloud auth print-identity-token)" \
  $FE_PROD_URL