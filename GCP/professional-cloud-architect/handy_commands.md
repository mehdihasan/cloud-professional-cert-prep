#### You can list the active account name with this command:

```bash
gcloud auth list
```

#### You can list the project ID with this command:

```bash
gcloud config list project
```

#### To see what your default region and zone settings are, run the following commands:

```bash
gcloud config get-value compute/zone
gcloud config get-value compute/region
```
```bash
gcloud config set compute/zone us-central1-a
gcloud config set compute/region us-central1
```

#### View the list of configurations in your environment:
```bash
gcloud config list
```
 
#### To see all properties and their settings:
```bash
gcloud config list --all
```

#### List your components:
```bash
gcloud components list
```

#### To connect to your VM with SSH, run the following command:
```bash
gcloud compute ssh <your_vm_name> --zone $ZONE
```


### Describe project
```bash
gcloud compute project-info describe --project <your_project_ID>
```

gcloud compute project-info describe --project qwiklabs-gcp-02-b95c84022052

export PROJECT_ID=qwiklabs-gcp-02-b95c84022052

export ZONE=us-central1-a