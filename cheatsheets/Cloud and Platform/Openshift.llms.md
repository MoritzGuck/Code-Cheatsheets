# OpenShift

OpenShift is a platform based on Kubernetes to host and manage applications in containers.

## Core concepts

### Software

#### Containers & pods

For containers, images, registries see [Docker cheatsheet](../../cheatsheets/CLI%20Tools/docker.llms.md).

Pods  
Smallest deployable units in Kubernetes/OpenShift. Can contain multiple containers that share network namespace, storage, and lifecycle. Pods are ephemeral and should be managed through higher-level controllers.

Pod lifecycle  
Managed through phases including Pending, Running, Succeeded, Failed, and Unknown. Pods have a restart policy; containers have individual health checks.

Services  
Abstract way to expose applications running in pods. Types include ClusterIP, NodePort, LoadBalancer, and ExternalName. Support features like service mesh integration.

Labels and annotations  
Key-value pairs for organizing and selecting Kubernetes resources. Annotations provide non-identifying metadata for automation and tooling.

Common commands:

| Description | Command |
|----|----|
| List pods in the current project | `oc get pods` |
| Show detailed pod info (events, probes, volumes) | `oc describe pod <pod-name>` |
| Follow pod logs | `oc logs -f <pod-name>` |
| Open an interactive shell in a pod | `oc rsh <pod-name>` |
| Execute a command in a running container | `oc exec <pod-name> -- <command>` |
| Forward a local port to a pod | `oc port-forward <pod-name> <local-port>:<container-port>` |
| Restart a pod (controller recreates it) | `oc delete pod <pod-name>` |

#### Projects & users

Users  
Identity management integrated with enterprise systems (LDAP, OAuth, etc.). Supports Role-Based Access Control (RBAC) with custom roles and bindings.

Kubernetes namespaces  
Virtual clusters within a physical cluster. Support resource quotas, network policies, and service isolation. Enable multi-tenancy and environment separation.

Projects  
OpenShift’s extension of namespaces with additional security controls, routing, and monitoring features. Support for project templates and operator integration.

Common commands:

| Description | Command |
|----|----|
| Log in to a cluster | `oc login <api-url>` |
| Show the current user | `oc whoami` |
| Show the current project | `oc project` |
| List accessible projects | `oc projects` |
| Create and switch to a new project | `oc new-project <name>` |
| Switch to another project | `oc project <name>` |
| Show an overview of the current project | `oc status` |
| Grant a user a role in the current project | `oc adm policy add-role-to-user <role> <user>` |
| List your effective permissions | `oc auth can-i --list` |

#### Builds & Deployments

Image streams  
OpenShift abstraction for managing container images. Support automatic updates, rollbacks, and deployment triggers. Enable image promotion across environments.

Jobs and CronJobs  
Manage pods that run to completion. Support for parallel processing, retry policies, and scheduled execution. Integration with cluster autoscaling.

Operators  
Software extensions to Kubernetes that use custom resources to manage applications and their components. Automate operational tasks and complex deployments.

Common commands:

| Description | Command |
|----|----|
| Create an application from source code or an image | `oc new-app <source-or-image>` |
| List builds | `oc get builds` |
| Start a build manually | `oc start-build <buildconfig-name>` |
| Follow build logs | `oc logs -f bc/<buildconfig-name>` |
| List image streams | `oc get imagestreams` |
| Import an image into an image stream | `oc import-image <imagestream-name>` |
| Tag an image into an image stream | `oc tag <source-image> <target>[:<tag>]` |
| Watch a rollout’s progress | `oc rollout status deploy/<name>` |
| Roll back to the previous revision | `oc rollout undo deploy/<name>` |
| Scale a deployment | `oc scale deploy/<name> --replicas=<n>` |

### Infrastructure

Nodes  
Physical or virtual machines running containerized workloads. Support various runtime classes and compute profiles. Can be managed through machine sets and autoscaling.

Clusters  
Distributed systems for container orchestration. Support hybrid and multi-cloud deployments and advanced networking models.

Service mesh  
Infrastructure layer for service-to-service communication. Provides traffic management, security, and observability features.

Common commands:

| Description | Command |
|----|----|
| List cluster nodes | `oc get nodes` |
| Show node resource usage | `oc adm top nodes` |
| List machine sets | `oc get machinesets -n openshift-machine-api` |
| Show installed cluster version and update channel | `oc get clusterversion` |
| List operators and their health | `oc get clusteroperators` |

More information:

- [OpenShift: Core Concepts](https://docs.openshift.com/container-platform/latest/architecture/architecture.html)
- [Google Cloud: What are Containers](https://cloud.google.com/learn/what-are-containers)
- [Docker Docs: Docker overview](https://docs.docker.com/get-started/docker-overview/)
