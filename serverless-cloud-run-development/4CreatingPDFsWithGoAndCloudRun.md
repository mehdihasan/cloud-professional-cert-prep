# Creating PDFs with Go and Cloud Run

gcloud services enable cloudbuild.googleapis.com

gcloud services enable storage-component.googleapis.com

gcloud services enable run.googleapis.com


# Create a Service Account

gsutil notification create -t new-doc -f json -e OBJECT_FINALIZE gs://$GOOGLE_CLOUD_PROJECT-upload

gcloud iam service-accounts create pubsub-cloud-run-invoker --display-name "PubSub Cloud Run Invoker"

gcloud run services add-iam-policy-binding pdf-converter \
  --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com \
  --role=roles/run.invoker \
  --region us-central1 \
  --platform managed

PROJECT_NUMBER=$(gcloud projects list \
 --format="value(PROJECT_NUMBER)" \
 --filter="$GOOGLE_CLOUD_PROJECT")

gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member=serviceAccount:service-$PROJECT_NUMBER@gcp-sa-pubsub.iam.gserviceaccount.com \
  --role=roles/iam.serviceAccountTokenCreator

## Testing the Cloud Run service

SERVICE_URL=$(gcloud run services describe pdf-converter \
  --platform managed \
  --region us-central1 \
  --format "value(status.url)")


echo $SERVICE_URL


curl -X GET $SERVICE_URL


curl -X GET -H "Authorization: Bearer $(gcloud auth print-identity-token)" $SERVICE_URL


## Cloud Storage trigger

gcloud pubsub subscriptions create pdf-conv-sub \
  --topic new-doc \
  --push-endpoint=$SERVICE_URL \
  --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com



## Testing Cloud Storage Notification

gsutil -m cp -r gs://spls/gsp762/* gs://$GOOGLE_CLOUD_PROJECT-upload

