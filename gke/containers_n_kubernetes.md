# Containers and Kubernetes

## Objectives
- Creating container - using `Cloud Build`
- Store container - using `Container Registry`
- Compare & contrast K8s and GKE features.


## Container
r isolated user spaces for running application code. Containarization is the next level of evolution of managing code. Containers are running instance of an image.

```diagram
 _____________________          _____________________
|                     |        |                     |
|      CONTAINER      |        |      CONTAINER      |
|                     |        |                     |
|   Application Code  |        |   Application Code  |
|                     |        |                     |
|     Dependencies    |        |     Dependencies    |
 _____________________          _____________________
---------------------------------------------------------
            C O N T A I N E R   R U N T I M E
---------------------------------------------------------
                      K E R N E L
---------------------------------------------------------
                    H A R D W A R E
---------------------------------------------------------
```

### Container base technologies (Linux):
1. **Processes**: own virtual memory address space  
2. **Linux namespaces**: to control what an application can see, i.e. ip adresses, directory tree etc.
3. **Linux cgroups**: to control what an application can use, i.e. cpu, i/o bandwidth etc.
4. **Union file system**: to efficienctly encapsulate applications and their dependencies into a set of clean minimal layer.
** containerd: container runtime for docker.


### Container image layers:
1. **Base image layers / Read-only layers**: Each dockerfile command will going to create a layer in the container image. The topmost dockerfile command will going to be the 1st/base layer. 
2. These layers are read only. 
3. **Container layer / Ephemeral r/w layer**: When the a container has been created out of a image, there created a topmost thin ephemeral readble/writable layer. This layer will be deleted when the container removed.
4. **Multi stage build:** Now a days, image build process are multi staged. In the first stage, the artifact is being build. In the second stage another image is being build with the executable image and the dependencies.

![contaainer-image-layer](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.mhMXzMmN3Cpi15YwjjHM8gHaEi%26pid%3DApi&f=1)


## Concepts
> problems are containers intended to solve?

> Why do Linux containers use union file systems?
- to efficienctly encapsulate applications and their dependencies into a set of clean minimal layer.

> What is significant about the topmost layer in a container?
- When the a container has been created out of a image, there created a topmost thin ephemeral readble/writable layer. This layer will be deleted when the container removed.

> What does it mean by the topmost layer? Base image?
- Not base image. But Container layer / Ephemeral r/w layer.

> Declarative configuration VS  Imperative configuration?
- **Declarative configuration:** define the desired state in a declarative manner in a file. K8s job is to maintain that state. 
- **Imperative configuration:** Engineer issues command to change the system state. Better to use it for quick temporary fixes or and as a tool for building declarative configuration. 



## Weak Points
1. [Compute Options](https://googlecourses.qwiklabs.com/course_sessions/483162/video/102915)