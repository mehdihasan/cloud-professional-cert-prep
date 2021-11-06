# Kubernetes Workloads

## Objective
- `kubectl` command
- k8s deployments
- networking architectures of Pods
- k8s storage abstractions

## kubectl
1. How does a `kubectl` command works in the background
1. configure `kubectl`
    - $HOME/.kube/config
    - view current config
        ```bash
        kubectl config view
        ```
    - connecting to a GKE cluster
        ```bash
        gcloud container clusters \
        get-credentials [CLUSTER_NAME] \
        --zone [ZONE_NAME]
        ```
    - `kubectl` command syntax
        - `kubectl [COMMAND] [TYPE] [NAME] [flags]`
        - [command]: get / describe / logs / exec
        - [type]: pods / deployments / nodes
        - [name]: object name
        - [flags]: any special request? [-o=yaml] [-o=wide]


## deployments
- declare the state of pods
- import a deployment into a file
    ```bash
    kubectl get deployment [DEPLOYMENT_NAME] -o yaml > this.yaml
    ```
- detail of the deployment
    ```bash
    kubectl describe deployment [DEPLOYMENT_NAME]
    ```
- Scaling
    ```bash
    kubectl scale deployment [DEPLOYMENT_NAME] -replicas=5
    ```
- Autoscale Deployment
    ```bash
    kubectl autoscale deployment [DEPLOYMENT_NAME] --min=5 --max=15 --cpu-percent=75
    ```
- Edit a deployment
    ```bash
    kubectl edit deployment/[DEPLOYMENT_NAME]
    ```