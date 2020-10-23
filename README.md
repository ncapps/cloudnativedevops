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
### 1. Revolution in the Cloud
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

### 2. First Steps with Kubernetes
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
