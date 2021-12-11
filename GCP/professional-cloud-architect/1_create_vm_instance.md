# Create a new instance with gcloud

### In the Cloud Shell, use gcloud to create a new virtual machine instance from the command line:
```bash
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone us-central1-f
```

### To see all the defaults, run:

```bash
gcloud compute instances create --help
```