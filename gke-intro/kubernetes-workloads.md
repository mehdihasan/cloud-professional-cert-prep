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


### [Blue/green deployment](https://googlecourses.qwiklabs.com/course_sessions/483162/video/102947)

> does the blue/green deployment work for Strimzi Kafka Connect - with running pipelines?

![blue_green_deployment](https://cdn.qwiklabs.com/POW8Q247ZKNY%2ByHIartCsoEu8MAih7k4u1twusCx6pw%3D)

Rolling updates are ideal because they allow you to deploy an application slowly with minimal overhead, minimal performance impact, and minimal downtime. There are instances where it is beneficial to modify the load balancers to point to that new version only after it has been fully deployed. In this case, blue-green deployments are the way to go.

Kubernetes achieves this by creating two separate deployments; one for the old "blue" version and one for the new "green" version. Use your existing hello deployment for the "blue" version. The deployments will be accessed via a Service which will act as the router. Once the new "green" version is up and running, you'll switch over to using that version by updating the Service.

The technique is to make a new deployment with a new version selector. After the deployment is successful, need to update and deply the service object with the new selector.

```yaml
[...]
kind: Service
spec:
    selector:
        app: my-app
        version: v2
[...]
```

### [Canary Deployments](https://googlecourses.qwiklabs.com/course_sessions/483162/video/102948)

![canary_deployment](https://cdn.qwiklabs.com/qSrgIP5FyWKEbwOk3PMPAALJtQoJoEpgJMVwauZaZow%3D)

When you want to test a new deployment in production with a subset of your users, use a canary deployment. Canary deployments allow you to release a change to a small subset of your users to mitigate risk associated with new releases.

A canary deployment consists of a separate deployment with your new version and a service that targets both your normal, stable deployment as well as your canary deployment.

