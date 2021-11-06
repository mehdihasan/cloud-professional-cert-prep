
gcloud auth list

gcloud config list project

## Enable the Cloud Run API



# The command builds a container with your code and puts it in the Container Registry of your project.

gcloud builds submit \
  --tag gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter

# deploy your application to cloud run

gcloud run deploy pdf-converter \
  --image gcr.io/$GOOGLE_CLOUD_PROJECT/pdf-converter \
  --platform managed \
  --region us-central1 \
  --no-allow-unauthenticated \
  --max-instances=1


# Create the environment variable $SERVICE_URL for the app so you can easily access it:

SERVICE_URL=$(gcloud beta run services describe pdf-converter --platform managed --region us-central1 --format="value(status.url)")

echo $SERVICE_URL

# Post request with Auth

curl -X POST -H "Authorization: Bearer $(gcloud auth print-identity-token)" $SERVICE_URL


PROJECT_NUMBER=579332740733