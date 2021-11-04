# Anthos & Migration

1. local computers: on-premses/cloud
2. Migration Manager / Edge Nodes / Cache: Migrate for Compute Engine
3. GKE - Processing Cluster / Cloud Storage - Artifacts / Con: Migrate for Anthos
4. Production project


## Steps
1. Configure processing cluster. Install migration for Anthos components into that cluster. Also need to enable `firewall` rules for migrate form Anthos and migrate for compute engine.
    ```bash
    gcloud container --project $PROJECT_ID \
    clusters create $CLUSTER_NAME \
    --zone $CLUSTER_ZONE \
    --username "admin" \
    --cluster-version 1.14 \
    --machine-type "n1-standard-4" \
    --image-type "UBUNTU" \
    --num-nodes 1 \
    --enable-stackdriver-kubernetes \
    --scopes "cloud-platform" \
    --enable-ip-alias \
    --tags="http-server"
    ```
    Now, need to instal migration for Anthos. It will going to instal all the required k8s resources into the processing cluster for the migration.
    ```bash
    migctl setup install
    ```

2. Add migration source - VMWare, Azure, AWS
    ```bash
    migctl source create ce my-ce-source --project my-project --zone zone
    ```

3. Generate and review plan
    ```bash
    migctl migration create test-migration --source my-ce-src --vm-id my-id --intent Image
    ```

4. Generate artifacts: container images and deployment files
    ```bash
    migctl migration generate-artifacts my-migration
    ```
    will generate - container image into container registry, and YAML files for deployment.
    To download the generated YAML files, run the following commands:
    ```bash
    migctl migration get-artifacts test-migration
    ```

5. Test - both the container images and deployments
    ```bash
    kubectl apply -f deployment_spec.yaml
    ```

6. Deploy - generated artifacts to the production cluster