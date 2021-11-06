# Build a Resilient, Asynchronous System with Cloud Run and Pub/Sub 

gcloud config set project qwiklabs-gcp-01-035ea1bee438

## Create a Pbusub topic

GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-01-035ea1bee438


gcloud pubsub topics create new-lab-report --project=qwiklabs-gcp-01-035ea1bee438


gcloud services enable run.googleapis.com


gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/lab-report-service \
  --project=$GOOGLE_CLOUD_PROJECT

gcloud run deploy lab-report-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/lab-report-service \
  --platform managed \
  --region us-central1 \
  --allow-unauthenticated \
  --max-instances=1 \
  --project=$GOOGLE_CLOUD_PROJECT


export LAB_REPORT_SERVICE_URL=$(gcloud run services describe lab-report-service --platform managed --region us-central1 --format="value(status.address.url)" --project=$GOOGLE_CLOUD_PROJECT)


deploy.sh


gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/email-service \
  --project=$GOOGLE_CLOUD_PROJECT

gcloud run deploy email-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/email-service \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1 \
  --project=$GOOGLE_CLOUD_PROJECT



## Create the Pub/Sub subscription 

gcloud iam service-accounts create pubsub-cloud-run-invoker --display-name "PubSub Cloud Run Invoker" --project=$GOOGLE_CLOUD_PROJECT


gcloud run services add-iam-policy-binding email-service --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-central1 --platform managed --project=$GOOGLE_CLOUD_PROJECT


PROJECT_NUMBER=$(gcloud projects list --filter="qwiklabs-gcp" --format='value(PROJECT_NUMBER)' --project=$GOOGLE_CLOUD_PROJECT)


gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT --member=serviceAccount:service-$PROJECT_NUMBER@gcp-sa-pubsub.iam.gserviceaccount.com --role=roles/iam.serviceAccountTokenCreator --project=$GOOGLE_CLOUD_PROJECT


EMAIL_SERVICE_URL=$(gcloud run services describe email-service --platform managed --region us-central1 --format="value(status.address.url)" --project=$GOOGLE_CLOUD_PROJECT)


gcloud pubsub subscriptions create email-service-sub --topic new-lab-report --push-endpoint=$EMAIL_SERVICE_URL --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --project=$GOOGLE_CLOUD_PROJECT




## Deploy the SMS Service

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/sms-service \
  --project=$GOOGLE_CLOUD_PROJECT

gcloud run deploy sms-service \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/sms-service \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1 \
  --project=$GOOGLE_CLOUD_PROJECT


gcloud run services add-iam-policy-binding sms-service --member=serviceAccount:pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --role=roles/run.invoker --region us-central1 --platform managed --project=$GOOGLE_CLOUD_PROJECT


SMS_SERVICE_URL=$(gcloud run services describe sms-service --platform managed --region us-central1 --format="value(status.address.url)" --project=$GOOGLE_CLOUD_PROJECT)


echo $SMS_SERVICE_URL


gcloud pubsub subscriptions create sms-service-sub --topic new-lab-report --push-endpoint=$SMS_SERVICE_URL --push-auth-service-account=pubsub-cloud-run-invoker@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com --project=$GOOGLE_CLOUD_PROJECT

~/pet-theory/lab05/lab-service/post-reports.sh