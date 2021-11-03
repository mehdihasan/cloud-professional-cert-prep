# Kubernetes Architecture


## Objectives
- k8s objects
- [k8s control plane](https://googlecourses.qwiklabs.com/course_sessions/483162/video/102921)
- deploy k8s in GKE
- deploy pods in GKE
- view and manage k8s objects


## k8s objects
Each thing k8s manages can be represented as an object.
1. **object spec**: desired state described by us
2. **object ststus**: current state descrbed by k8s

Each object has a certain kind or type, i.e. pod, deployment, service. Pod is the basic building block. 

```diagram
_________________________________________________________
                        P   O   D
---------------------------------------------------------
                    Shared Networking
---------------------------------------------------------
| Container 1 | Container 2 | Container 3 | Container 4 |                    
---------------------------------------------------------
                     Shared Storage
---------------------------------------------------------
_________________________________________________________
```

## Controller Objects

- **ReplicaSets**: A ReplicaSet controller ensures that a population of Pods, all identical to one another, are running at the same time. Deployments let you do declarative updates to ReplicaSets and Pods. In fact, Deployments manage their own ReplicaSets to achieve the declarative goals you prescribe, so you will most commonly work with Deployment objects.
- **Deployment**: Deployments let you create, update, roll back, and scale Pods, using ReplicaSets as needed to do so. For example, when you perform a rolling upgrade of a Deployment, the Deployment object creates a second ReplicaSet, and then increases the number of Pods in the new ReplicaSet as it decreases the number of Pods in its original ReplicaSet. 
- **Replication Controllers**: Replication Controllers perform a similar role to the combination of ReplicaSets and Deployments, but their use is no longer recommended. Because Deployments provide a helpful "front end" to ReplicaSets.
- **StatefulSets**: If you need to deploy applications that maintain local state, StatefulSet is a better option. A StatefulSet is similar to a Deployment in that the Pods use the same container spec. The Pods created through Deployment are not given persistent identities, however; by contrast, Pods created using StatefulSet have unique persistent identities with stable network identity and persistent disk storage.
- **DaemonSets**: To run certain Pods on all the nodes within the cluster or on a selection of nodes, use DaemonSet. DaemonSet ensures that a specific Pod is always running on all or some subset of the nodes. If new nodes are added, DaemonSet will automatically set up Pods in those nodes with the required specification. The word "daemon" is a computer science term meaning a non-interactive process that provides useful services to other processes. A Kubernetes cluster might use a DaemonSet to ensure that a logging agent like fluentd is running on all nodes in the cluster.
- **Jobs**: The Job controller creates one or more Pods required to run a task. When the task is completed, Job will then terminate all those Pods. A related controller is CronJob, which runs Pods on a time-based schedule. 


## Service
Services provide load-balanced access to specified Pods. There are three primary types of Services: 
- **ClusterIP:** Exposes the service on an IP address that is only accessible from within this cluster. This is the default type. 
- **NodePort:** Exposes the service on the IP address of each node in the cluster, at a specific port number. 
- **LoadBalancer:** Exposes the service externally, using a load balancing service provided by a cloud provider.  

> In Google Kubernetes Engine, LoadBalancers give you access to a regional Network Load Balancing configuration by default. To get access to a global HTTP(S) Load Balancing configuration, you can use an Ingress object. 


## k8s object management
Reference example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx
    labels:
        app: nginx
        env: dev
        stack: frontend
spec: 
    replicas: 3
    template: 
        metadata:
            labels:
                app: nginx
        spec:
            containers:
            - name: nginx
              image: nginx:latest
    selector:
        matchLabels
        app: nginx
```

- **apiVersion:** which version of the Kubernetes API is used to create the object. backward compitable.
- **kind:** defines the kind of object.
- **metadata:** helps identifying the object using name, uid, nmaespace(optional)
    - **name:** all objects identified by a name. Cannot have 2 of the same type of objects with same names.
    - **labels:** labels are key value pairs to tag k8s objects during or after their creation labels to identify and organize objects and subsets of objects.
        - like in the following command it is loong for any label key `app` which value is `nginx`
        ```bash
        kubectl get pods -seclector=app=nginx
        ```

### Best Practices
- define several releated K8s objecs into the same YAML file
- saving the YAML files in version control system


## References
1. [k8s control plane and components](https://kubernetes.io/docs/concepts/overview/components/)