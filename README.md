# Cloud Native DevOps with Kubernetes

![Cloud Native DevOps cover image](/img/cover.jpg)

Welcome! This is the example code repository to accompany the book 'Cloud Native DevOps with Kubernetes', by John Arundel and Justin Domingus. Buy the book here:

* [Amazon US](https://amzn.to/2PEPTjc)

* [Amazon UK](https://amzn.to/2PGkZa0)

* [O'Reilly](http://shop.oreilly.com/product/0636920175131.do)

## About the book

From the preface:

> You'll learn what Kubernetes is, where it comes from, and what it means for the future of software development and operations. You'll learn how containers work, how to build and manage them, and how to design cloud native services and infrastructure.

> You'll understand the trade-offs between building and hosting Kubernetes clusters yourself, and using managed services. You'll learn the capabilities, limitations, and pros and cons of popular Kubernetes installation tools such as kops, `kubeadm`, and Kubespray. You'll get an informed overview of the major managed Kubernetes offerings from the likes of Amazon, Google, and Microsoft.

> You'll get hands-on practical experience of writing and deploying Kubernetes applications, configuring and operating Kubernetes clusters, and automating cloud infrastructure and deployments with tools such as Helm. You'll learn about Kubernetes support for security, authentication, and permissions, including Role-Based Access Control (RBAC), and best practices for securing containers and Kubernetes in production.

> You'll learn how to set up continuous integration and deployment with Kubernetes, how to back up and restore data, how to test your cluster for conformance and reliability, how to monitor, trace, log, and aggregate metrics, and how to make your Kubernetes infrastructure scalable, resilient, and cost-effective.

The book aims to teach you everything you need to know to deploy, run, and scale applications in Kubernetes, and most importantly, to give you working example code for everything we demonstrate. That code is open source, available for free for you to use and adapt whether or not you buy the book. And here it is!

## Show me the code

Almost all the example code involves our 'hello world' demo application. Here is the list of examples; follow the links to see the documentation on each example.

* [Build and run the demo application locally](/hello) (start here!)
* [Deploy the app to Kubernetes using kubectl](/hello-k8s)
* [Deploy the app with Helm](/hello-helm) (or [with Helm 3)](/hello-helm3)
* [Manage the app with Helmfile](/hello-helmfile)
* [Set up a namespace, resource requests, and limits for the app](/hello-namespace)
* [Add config data to the app's environment](/hello-config-env)
* [Pass config data to the app's command line](/hello-config-args)
* [Mount a config file in the container at runtime](/hello-config-file)
* [Add secret data to the app's environment](/hello-secret-env)
* [Mount a secrets file in the container](/hello-secret-file)
* [Store encrypted secrets in the app repo, decrypted automatically on deploy](/hello-sops)
* [Develop the app locally with Skaffold](/hello-skaffold)
* [Build and deploy the app automatically with Drone](/hello-drone)
* [Build and deploy the app automatically with Google Cloud Build](/hello-cloudbuild)

## Terraform examples

We also include some Terraform code examples, to help you manage cloud resources with code. Unfortunately we didn't have space to discuss these in the book, but we hope they'll be useful to you anyway.

### Google Cloud

* [Create a cloud storage bucket in code](/terraform/gcp/bucket)
* [Create a cloud database instance in code](/terraform/gcp/database)
* [Create a Kubernetes cluster in code](/terraform/gcp/k8scluster)

### Amazon AWS

* [Create a cloud storage bucket in code](/terraform/aws/bucket)
* [Create a cloud database instance in code](/terraform/aws/database)
* [Create a cloud virtual machine instance in code](/terraform/aws/vm)

## You will need

To build and run all of these examples, you will need:

* Go (any recent version is fine)
* Docker version 18.03 or above

Where you need other tools for specific examples, we'll mention that in the README for the example.

## Contributing to the repo

We would absolutely love it if you contributed! Feel free to send us a PR to add new examples, add versions of the examples for different cloud providers (for example Microsoft Azure), or fix or improve the existing examples.

## Known Issues

### apiVersion

When we released the book most k8s clusters still used `extensions/v1beta1` for Deployments. On newer versions of k8s `Deployment` has been moved to `apiVersion: apps/v1`. If you get the message:

```
error: unable to recognize "k8s/deployment.yaml": no matches for kind "Deployment" in version "extensions/v1beta1"
```

when trying out the examples then try updating `apiVersion: extensions/v1beta1` to `apiVersion: apps/v1` in your `deployment.yaml` file(s).

Thanks to @thescott for pointing this out.

### Helm 3

At the time of publishing Helm 3 was not yet released and we included the Helm 2 examples that included the additional steps of installing `tiller`.

Helm 3 is now released and `tiller` is no longer required.

We have added the `hello-helm3` examples here with updated instructions if you would like to use the latest version of Helm.

You can read more about the changes between version 2 and 3 [here](https://v3.helm.sh/docs/faq/#changes-since-helm-2).

### `kubectl run` and Pod/Deployment v1.18 change

In the 1.18 release of Kubernetes the `kubectl run` command changed from creating a `Deployment` by default to creating a `Pod` instead.

We have a few examples where we use `kubectl run` to get familiar with running a container in k8s.

Later we discuss why using the declarative `kubectl apply -f...` is preferred over the imperative `create`, `edit` or `run`, because your version-controlled YAML files always reflect the real state of the cluster.

In our `kubectl run` example we show the output as `deployment.apps "demo" created` but on version 1.18 instead you will instead see `pod/demo created`.

The subsequent port-forward example would instead be: `kubectl port-forward pod/demo 9999:8888`

Again, using `kubectl apply -f...` and keeping your manifests tracked in source control is a better long-term solution.

### Service port 8888 VS 9999

Depending on which version of the book you read you may see reference to using port `9999` for the service port [here](https://github.com/cloudnativedevops/demo/blob/master/hello-k8s/k8s/service.yaml). This caused some confusion between the pod port, service port, and the port-forwarding port in the examples, so was changed in the latest revision to use `8888` for both the pod and the serivce. Thanks to @randoljt for catching this and sorry for any confusion.

## Notes from the book
### Chapter 1. Revolution in the Cloud
**Cloud Native characteristics**
- *Automatable*: If applications are to be deployed and managed by machines, instead of humans, they need to abide by common standards, formats, and interfaces. Kubernetes provides these standard interfaces in a way that means application developers don’t even need to worry about them.

- *Ubiquitous and flexible*: Because they are decoupled from physical resources such as disks, or any specific knowledge about the compute node they happen to be running on, containerized microservices can easily be moved from one node to another, or even one cluster to another.

- *Resilient and scalable*: Traditional applications tend to have single points of failure: the application stops working if its main process crashes, or if the underlying machine has a hardware failure, or if a network resource becomes congested. Cloud native applications, because they are inherently distributed, can be made highly available through redundancy and graceful degradation.

- *Dynamic*: A container orchestrator such as Kubernetes can schedule containers to take maximum advantage of available resources. It can run many copies of them to achieve high availability, and perform rolling updates to smoothly upgrade services without ever dropping traffic.

- *Observable*: Cloud native apps, by their nature, are harder to inspect and debug. So a key requirement of distributed systems is observability: monitoring, logging, tracing, and metrics all help engineers understand what their systems are doing (and what they’re doing wrong).

- *Distributed*: Cloud native is an approach to building and running applications that takes advantage of the distributed and decentralized nature of the cloud. It’s about how your application works, not where it runs. Instead of deploying your code as a single entity (known as a monolith), cloud native applications tend to be composed of multiple, cooperating, distributed microservices. A microservice is simply a self-contained service that does one thing. If you put enough microservices together, you get an application.

**Summary**
- *DevOps* brings a code-centric approach and good software engineering practices to the world of infrastructure and operations.
- *Containers* allow you to deploy and run software in small, standardized, self-contained units. This makes it easier and cheaper to build large, diverse, distributed systems, by connecting together containerized microservices.
- *Orchestration systems* take care of deploying your containers, scheduling, scaling, networking, and all the things that a good system administrator would do, but in an automated, programmable way.
- **Kubernetes** is the de facto standard container orchestration system
- *Cloud native* is a useful shorthand for talking about cloud-based, containerized, distributed systems, made up of cooperating microservices, dynamically managed by automated infrastructure as code.

### Chapter 2. First Steps with Kubernetes
**Docker basics**
```bash
cd hello/

# Build docker image
docker image build -t myhello .

# Run docker container
# docker container run -p HOST_PORT:CONTAINER_PORT ...
docker container run -p 9999:8888 myhello

# Authenticate with Docker Hub
docker login

# Name and publish image
docker image tag myhello ncapps/myhello
docker image push ncapps/myhello
```

**Introducing Kubernetes**
```bash
# Run the demo app
k run demo --image=ncapps/myhello --port=9999 --labels app=demo

# Forward port 9999 on your local machine to the container's port 8888
k port-forward pod/demo 9999:8888

# Get pods
k get pods --selector app=demo
```

**Summary**
- The `kubectl` tool is the primary way of interacting with a Kubernetes cluster.
- It can be used either imperatively (to run a public container image, for example, and implicitly creating the necessary Kubernetes resources).
- It can be used declaratively, to apply Kubernetes configuration in the form of YAML manifests.

### Chapter 3. Getting Kubernetes
**Cluster Architecture**
- *The Control Plane*: Runs all the tasks required for Kubernetes to do its job: scheduling containers, managing Services, serving API requests, etc. The members of the cluster which run the control plane components are called *master nodes*.
- *Worker nodes*: Cluster member that run user workloads

**Control Plane Components**
- *kube-apiserver*: This is the frontend server for the control plane, handling API requests.
- *etcd*: This is the database where Kubernetes stores all its information: what nodes exist, what resources exist on the cluster, and so on.
- *kube-scheduler*: This decides where to run newly created Pods.
- *kube-controller-manager*: This is responsible for running resource controllers, such as Deployments.
- **cloud-controller-manager*: This interacts with the cloud provider (in cloud-based clusters), managing resources such as load balancers and disk volumes.

**Node Components**
- *kubelet*: This is responsible for driving the container runtime to start workloads that are scheduled on the node, and monitoring their status.
- *kube-proxy*: This does the networking magic that routes requests between Pods on different nodes, and between Pods and the internet.
- *Container runtime*: This actually starts and stops containers and handles their communications. Usually Docker, but Kubernetes supports other container runtimes, such as rkt and CRI-O.


**Kubernetes Installers**
- *Rancher Kubernetes Engine (RKE)* aims to be a simple, fast Kubernetes installer. It doesn’t provision the nodes for you, and you have to install Docker on the nodes yourself before you can use RKE to install the cluster. RKE supports high availability of the Kubernetes control plane.
For learning: https://github.com/kelseyhightower/kubernetes-the-hard-way

**Run Less Software**
There are three pillars of the Run Less Software philosophy, all of which will help you manipulate time and defeat your enemies.

1. Choose standard technology
2. Outsource undifferentiated heavy lifting
3. Create enduring competitive advantage

**Summary**
- It’s not worth managing Kubernetes clusters yourself if a service provider can do it better and cheaper.
- You should use managed Kubernetes if you can. This is the best option for most businesses in terms of cost, overhead, and quality.
- Don’t self-host your cluster without sound business reasons. If you do self-host, don’t underestimate the engineering time involved for the initial setup and ongoing maintenance overhead.

### Chapter 4. Working with Kubernetes Objects
**Deployments**
- A Deployment’s job is to watch its associated containers and make sure that the specified number of them is always running.
```
# Query all active Deployments
k get depoyments

# Get detailed information on a specific deployment
k describe deployments/demo
```

**Pods**
- A Pod is the Kubernetes object that represents a group of one or more containers
- Sometimes a set of containers needs to be scheduled together, running on the same node, and communicating locally, perhaps sharing storage.
- Deployments don’t manage Pods directly. That’s the job of the ReplicaSet object. Deployments manage ReplicaSets.

**ReplicaSets**
- A ReplicaSet is responsible for a group of identical Pods, or replicas. If there are too few (or too many) Pods, compared to the specification, the ReplicaSet controller will start (or stop) some Pods to rectify the situation.
```
# Check the pod
k get pods --selector app=demo

# Stop the pod
k delete pods --selector app=demo

# Clean up by tag
k delete all --selector app=demo
```

**Deployment Manifests**
```
cd hello-k8s

# Create a deployment
k apply -f k8s/deployment.yaml

# Check that the pod is running
k get pods --selector app=demo
```

**Service Resources**
- A Service resource gives you a single, unchanging IP address or DNS name that will be automatically routed to any matching Pod
- Think of a Service as being like a web proxy or a load balancer, forwarding requests to a set of backend Pods
- The *selector* is the part that tells the Service how to route requests to particular Pods.
- Requests will be forwarded to any Pods matching the specified set of labels; in this case, just app: demo
- A Deployment manages a set of Pods for your application, and a Service gives you a single entry point for requests to those Pods.
```
# Create a service
k apply - f k8s/service.yaml

# Forward port
k port-forward service/demo 9999:8888

# Clean up
k delete -f k8s/
```

**Querying the Cluster with kubectl**
- The `kubectl` tool is the Swiss Army knife of Kubernetes: it applies configuration, creates, modifies, and destroys resources
```
# See all resources by type
k get all

# Get detailed information on a specific resource
# k describe <TYPE>/<NAME>
```

**Helm: A Kubernetes Package Manager**
- You can use the helm command-line tool to install and configure applications (your own or anyone else’s)
- You can create packages called Helm charts, which completely specify the resources needed to run the application, its dependencies, and its configurable settings.
- A Helm chart is really just a convenient wrapper around Kubernetes YAML manifests.
```
# Creates depoyment and service objects
helm install demo ./k8s/demo
```

**Important Helm terms**
- *chart* is a Helm package, containing all the resource definitions necessary to run an application in Kubernetes.
- *repository* is a place where charts can be collected and shared.
- *release* is a particular instance of a chart running in a Kubernetes cluster.
```
# List Helm Releases
helm list

# See status for a particular release
helm status <NAME>

# Clean up
helm uninstall demo
```

[Public helm charts](https://github.com/helm/charts/tree/master/stable)


**Summary**
- The Pod is the fundamental unit of work in Kubernetes, specifying a single container or group of communicating containers that are scheduled together.
- A Deployment is a high-level Kubernetes resource that declaratively manages Pods, deploying, scheduling, updating, and restarting them when necessary.
- A Service is the Kubernetes equivalent of a load balancer or proxy, routing traffic to its matching Pods via a single, well-known, durable IP address or DNS name.
- The Kubernetes scheduler watches for a Pod that isn’t yet running on any node, finds a suitable node for it, and instructs the kubelet on that node to run the Pod.
- Resources like Deployments are represented by records in Kubernetes’s internal database. Externally, these resources can be represented by text files (known as manifests) in YAML format. The manifest is a declaration of the desired state of the resource.
- `kubectl` is the main tool for interacting with Kubernetes, allowing you to apply manifests, query resources, make changes, delete resources, and do many other tasks.
- Helm is a Kubernetes package manager. It simplifies configuring and deploying Kubernetes applications, allowing you to use a single set of values (such as the application name or listen port) and a set of templates to generate Kubernetes YAML files, instead of having to maintain the raw YAML files yourself.

### Chapter 5. Managing Resources
**Understanding Resources**
Always specify resource requests and limits for your containers. This helps Kubernetes schedule and manage your Pods properly.

- In order to schedule Pods effectively, the scheduler has to know the minimum and maximum allowable resource requirements for each Pod.
- Kubernetes understands how to manage two kinds of resources: CPU and memory.
- Kubernetes *resource request* specifies the minimum amount of that resource that the Pod needs to run
- A *resource limit* specifies the maximum amount of resource that a Pod is allowed to use.
- A Pod that tries to use more than its allocated CPU limit will be *throttled*, reducing its performance.
- A Pod that tries to use more than the allowed memory limit, though, will be terminated. If the terminated Pod can be rescheduled, it will be. In practice, this may mean that the Pod is simply restarted on the same node.
- Kubernetes allows resources to be *overcommitted*; that is, the sum of all the resource limits of containers on a node can exceed the total resources of that node. This is a kind of gamble: the scheduler is betting that, most of the time, most containers will not need to hit their resource limits.
Example resource requests
```yaml
spec:
  containers:
  - name: demo
    image: cloudnatived/demo:hello
    ports:
    - containerPort: 8888
    resources:
      requests:
        memory: "10Mi"
        cpu: "100m"
```

Example resource limits
```yaml
spec:
  containers:
  - name: demo
    image: cloudnatived/demo:hello
    ports:
    - containerPort: 8888
    resources:
      limits:
        memory: "20Mi"
        cpu: "250m"
```

**Managing the Container Life Cycle**
Use readiness probes and liveness probes to let Kubernetes know when your application is ready to handle requests, or when it has a problem and needs to be restarted.

- Kubernetes lets you specify a *liveness* probe as part of the container spec: a health check that determines whether or not the container is alive (that is, working).
- The `initialDelaySeconds` field lets you tell Kubernetes how long to wait before trying the first liveness probe, avoiding this loop of death situation.
- The `periodSeconds` field specifies how often the liveness probe should be checked
- The `exec` probe runs the specified command inside the container, and the probe succeeds if the command succeeds (that is, exits with a zero status). `exec` is usually more useful as a readiness probe.
- The exec probe runs the specified command inside the container, and the probe succeeds if the command succeeds (that is, exits with a zero status). exec is usually more useful as a readiness probe
- Readiness probes should only return HTTP 200 OK status
- A container or Pod will not be considered ready until its readiness probe has been up for `minReadySeconds` (default 0).
- Set PodDisruptionBudgets for your business-critical applications to make sure there are always enough replicas to maintain the service, even when Pods are evicted.
- Create separate namespaces for each of your applications or each logical component of your infrastructure. Don’t use the default namespace: it’s too easy to make mistakes.

Example liveness probe
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8888
  initialDelaySeconds: 3
  periodSeconds: 3
```

Example readiness probe
```yaml
readinessProbe:
  httpGet:
    path: /healthz
    port: 8888
  initialDelaySeconds: 3
  periodSeconds: 3
```

**Using Namespaces**
A Kubernetes namespace is a way of partitioning your cluster into separate subdivisions, for whatever purpose you like.

- Every Kubernetes Service has an associated DNS name that you can use to talk to it
- Service DNS names always follow this pattern: `SERVICE.NAMESPACE.svc.cluster.local`
- Use `ResourceQuotas` in each namespace to enforce a limit on the number of Pods that can run in the namespace.
- You can set default requests and limits for all containers in a namespace using a `LimitRange` resource.
- Use LimitRanges in each namespace to set default resource requests and limits for containers, but don’t rely on them; treat them as a backstop. Always specify explicit requests and limits in the container spec itself.

Example namespace
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo
```

Example LimitRange
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: demo-limitrange
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "256Mi"
    defaultRequest:
      cpu: "200m"
      memory: "128Mi"
    type: Container
```

**Optimizing Cluster Costs**
- Use the minimum number of Pods for a given Deployment that will satisfy your performance and availability requirements. Gradually reduce the number of replicas to just above the point where your service level objectives are met.
- There is a Kubernetes add-on called the Vertical Pod Autoscaler, which can help you work out the ideal values for resource requests.
- A good rule of thumb is that nodes should be big enough to run at least five of your typical Pods, keeping the proportion of stranded resources to around 10% or less. If the node can run 10 or more Pods, stranded resources will be below 5%.
- Larger nodes tend to be more cost-effective, because less of their resources are consumed by system overhead. Size your nodes by looking at real-world utilization figures for your cluster, aiming for between 10 and 100 Pods per node.
- Don’t use instance types with more storage than you need. Provision the smallest, lowest-IOPS disk volumes you can, based on the throughput and space that you actually use.
- Don’t use instance types with more storage than you need. Provision the smallest, lowest-IOPS disk volumes you can, based on the throughput and space that you actually use.

**Summary**
- Kubernetes allocates CPU and memory resources to containers on the basis of *requests* and *limits*
- A container’s requests are the minimum amounts of resources it needs to run. Its limits specify the maximum amount it’s allowed to use.
- Liveness probes tell Kubernetes whether the container is working properly. If a container’s liveness probe fails, it will be killed and restarted.
- Readiness probes tell Kubernetes that the container is ready and able to serve requests. If the readiness probe fails, the container will be removed from any Services that reference it, disconnecting it from user traffic.
- PodDisruptionBudgets let you limit the number of Pods that can be stopped at once during evictions, preserving high availability for your application.
- Namespaces are a way of logically partitioning your cluster. You might create a namespace for each application, or group of related applications.
- To refer to a Service in another namespace, you can use a DNS address like this: SERVICE.NAMESPACE.
- ResourceQuotas let you set overall resource limits for a given namespace.
- LimitRanges specify default resource requests and limits for containers in a namespace.
- Set resource limits so that your applications almost, but don’t quite exceed them in normal usage.




Implementation questions:
- Managing the `etcd` database of the Kubernetes control plane. What happens on reboot, interruption, etc.
- See "Self-hosted option" in Ch 3 of Cloud Native DevOps with Kubernetes
- Why not use managed kubernetes (EKS, Fargate) for the manager on the ground?
- How will we instruct kubernetes to clean up docker images?

