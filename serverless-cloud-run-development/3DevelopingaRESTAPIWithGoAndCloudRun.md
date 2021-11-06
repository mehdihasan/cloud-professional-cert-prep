# Developing a REST API with Go and Cloud Run

gcloud config set account student-03-99a44fd9413c@qwiklabs.net

gcloud config set project qwiklabs-gcp-03-6f31d02b4964

OR 

gcloud config set project $(gcloud projects list --format='value(PROJECT_ID)' --filter='qwiklabs-gcp')

gcloud services enable cloudbuild.googleapis.com

gcloud services enable run.googleapis.com


## Build Artifact

## Build image to the container registry

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1


## Deploy

gcloud run deploy rest-api \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1 \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --max-instances=2