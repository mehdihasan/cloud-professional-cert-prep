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

## References
1. [k8s control plane and components](https://kubernetes.io/docs/concepts/overview/components/)