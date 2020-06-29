## Kubernetes Clusters

[原文](https://kubernetes.io/docs/tutorials/kubernetes-basics/create-cluster/cluster-intro/)

A Kubernetes cluster consists of two types of resources:

- The Master coordinates the cluster
- Nodes are the workers that run applications

    **The Master is responsible for managing the cluster.** The master coordinates all activities in your cluster, such as scheduling applications, maintaining applications' desired state, scaling applications, and rolling out new updates.

    **A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster.** Each node has a Kubelet, which is an agent for managing the node and communicating with the Kubernetes master. The node should also have tools for handling container operations, such as Docker or rkt. A Kubernetes cluster that handles production traffic should have a minimum of three nodes. 最少3 个机器

    **The nodes communicate with the master using the Kubernetes API,** which the master exposes. End users can also use the Kubernetes API directly to interact with the cluster.

    A Kubernetes cluster can be deployed on either physical or virtual machines. To get started with Kubernetes development, you can use Minikube. **Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node.** Minikube is available for Linux, macOS, and Windows systems. The Minikube CLI provides basic bootstrapping operations for working with your cluster, including start, stop, status, and delete.

## minikube

go 语言写的minikube

minikube version

```sh
$ minikube version
minikube version: v1.8.1
commit: cbda04cf6bbe65e987ae52bb393c10099ab62014
```

minikube start # 启用cluster

```sh
minikube start # 启用cluster
$ minikube start
* minikube v1.8.1 on Ubuntu 18.04
* Using the none driver based on user configuration
* Running on localhost (CPUs=2, Memory=2460MB, Disk=145651MB) ...
* OS release is Ubuntu 18.04.4 LTS
* Preparing Kubernetes v1.17.3 on Docker 19.03.6 ...
  - kubelet.resolv-conf=/run/systemd/resolve/resolv.conf
* Launching Kubernetes ...
* Enabling addons: default-storageclass, storage-provisioner
* Configuring local host environment ...
* Waiting for cluster to come online ...
* Done! kubectl is now configured to use "minikube"
```

minikube status # 状态

```sh
$ minikube status
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
$
```

### hello minikube

#### create a Deployment

**A Kubernetes Pod is a group of one or more Containers, tied together for the purposes of administration and networking.** The Pod in this tutorial has only one Container. A Kubernetes Deployment checks on the health of your Pod and restarts the Pod's Container if it terminates. Deployments are the recommended way to manage the creation and scaling of Pods.

Use the `kubectl create` command to create a Deployment that manages a Pod. The Pod runs a Container based on the provided Docker image.

```sh
kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
```

View the Deployment:
```sh
kubectl get deployments

NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           1m
```

View the pods 

```sh
kubectl get pods
NAME                          READY     STATUS    RESTARTS   AGE
hello-node-5f76cf6ccf-br9b5   1/1       Running   0          1m
```

View cluster events:

```sh
kubectl get events
```

View the kubectl configuration:

```sh
kubectl config view
```

#### create a Service

Expose the Pod to the public internet using the kubectl expose command:

```sh
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```
The --type=LoadBalancer flag indicates that you want to expose your Service outside of the cluster.

View the Service you just created:

```sh
kubectl get services
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.108.144.78   <pending>     8080:30369/TCP   21s
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          23m
```
On cloud providers that support load balancers, an external IP address would be provisioned to access the Service. On Minikube, the `LoadBalancer` type makes the Service accessible through the `minikube service` command.

```sh
minikube service hello-node
```

#### clean up

Now you can clean up the resources you created in your cluster:

```sh
kubectl delete service hello-node
kubectl delete deployment hello-node
```
Optionally, stop the Minikube virtual machine (VM):

```sh
minikube stop
```
Optionally, delete the Minikube VM:

```sh
minikube delete
```

## kubectl


### kubectl version

和k8s 交互用kubectl。To interact with Kubernetes during this bootcamp we’ll use the command line interface, kubectl.
```sh
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.3", GitCommit:"06ad960bfd03b39c8310aaf92d1e7c12ce618213", GitTreeState:"clean", BuildDate:"2020-02-11T18:14:22Z", GoVersion:"go1.13.6", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.3", GitCommit:"06ad960bfd03b39c8310aaf92d1e7c12ce618213", GitTreeState:"clean", BuildDate:"2020-02-11T18:07:13Z", GoVersion:"go1.13.6", Compiler:"gc", Platform:"linux/amd64"}
```

The client version is the kubectl version; the server version is the Kubernetes version installed on the master.

### 查看cluster info，用kubectl cluster-info:

```sh
$ kubectl cluster-info
Kubernetes master is running at https://172.17.0.26:8443
KubeDNS is running at https://172.17.0.26:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

### 查看node

To view the nodes in the cluster, run the kubectl get nodes command:
```sh
$ kubectl get nodes
NAME       STATUS   ROLES    AGE     VERSION
minikube   Ready    master   9m59s   v1.17.3
```

This command shows all nodes that can be used to host our applications. Now we have only one node, and we can see that its status is ready (it is ready to accept applications for deployment).

ready 状态可以部署新的application

## 部署应用

Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. To do so, you create a Kubernetes Deployment configuration. The Deployment instructs Kubernetes how to create and update instances of your application. Once you've created a Deployment, the Kubernetes master schedules the application instances included in that Deployment to run on individual Nodes in the cluster.

Once the application instances are created, a Kubernetes Deployment Controller continuously monitors those instances. If the Node hosting an instance goes down or is deleted, the Deployment controller replaces the instance with an instance on another Node in the cluster. This provides a self-healing mechanism to address machine failure or maintenance.
创建一个Deployment 之后，集群会帮助你在某个node 上部署一个应用。而且如果部署的应用机器挂掉了，集群会再开一个node，保证了可用性。

用kubectl 去部署应用，kubectl 用的也是k8s API，与集群交互

You can create and manage a Deployment by using the Kubernetes command line interface, Kubectl. Kubectl uses the Kubernetes API to interact with the cluster.

初始的时候，需要指定镜像和分片个数（number of replicas），后面这些值也可以update
When you create a Deployment, you'll need to specify the container image for your application and the number of replicas that you want to run. You can change that information later by updating your Deployment

### pod

A Pod is the basic execution unit of a Kubernetes application. Each Pod represents a part of a workload that is running on your cluster. Learn more about Pods.

### deploy an app

You can use --help after the command to get additional info about possible parameters (kubectl get nodes --help).

### all nodes 

To view the nodes in the cluster, run the kubectl get nodes command:
```sh
$ kubectl get nodes
NAME       STATUS   ROLES    AGE     VERSION
minikube   Ready    master   7m51s   v1.17.3
```
Kubernetes will choose where to deploy our application based on Node available resources.
k8s 会选择一个空闲node 来部署应用

### 部署 kubectl create deployment

Let’s deploy our first app on Kubernetes with the `kubectl create deployment` command. We need to provide the deployment name and app image location (include the full repository url for images hosted outside Docker hub).
```sh
$ kubectl create deployment kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1
deployment.apps/kubernetes-bootcamp created
```

This performed a few things for you:

- searched for a suitable node where an instance of the application could be run (we have only 1 available node)
- scheduled the application to run on that Node
- configured the cluster to reschedule the instance on a new Node when needed

### 列出deployment

To list your deployments use the get deployments command:

```sh
$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1/1     1            1           61s
```
We see that there is 1 deployment running a single instance of your app. The instance is running inside a Docker container on your node.
一个deployment 在一个实例上运行，实例在node 的docker 容器里运行

### view application

Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same kubernetes cluster, but not outside that network. 
k8s 里的pods 是在私网里，默认情况是，pods 在集群内部可以被其它pods 和services 访问，但是不能在外部访问。但是不能在外部访问

When we use `kubectl`, we're interacting through an API endpoint to communicate with our application.
用kubectl 的时候，我们是用一个api endpoint，来和应用交互

The kubectl command can create a proxy that will forward communications into the cluster-wide, private network. The proxy can be terminated by pressing control-C and won't show any output while its running.
kubectl 可以创建一个代理，供外部使用

You can see all those APIs hosted through the proxy endpoint. For example, we can query the version directly through the API using the curl command:
有代理后，可以访问到proxy 代理的所有API。

```sh
$ curl http://localhost:8001/version
{
  "major": "1",
  "minor": "17",
  "gitVersion": "v1.17.3",
  "gitCommit": "06ad960bfd03b39c8310aaf92d1e7c12ce618213",
  "gitTreeState": "clean",
  "buildDate": "2020-02-11T18:07:13Z",
  "goVersion": "go1.13.6",
  "compiler": "gc",
  "platform": "linux/amd64"
}$
```

## view pods and nodes

### pods

When you created a Deployment in Module 2, Kubernetes created a Pod to host your application instance. A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker or rkt), and some shared resources for those containers. Those resources includes: 

- Shared storage, as Volumes
- Networking, as a unique cluster IP address
- Information about how to run each container, such as the container image version or specific ports to use

在创建Deployment 的时候，k8s 会创建一个pod 来host 你的应用实例。Pod 是k8s 中抽象概念，代表一组容器，和一些他们共享的资源。资源有：
- 共享的存储
- 网络，唯一集群ip 地址
- 容器运行方式，镜像，端口等等

Pods are the atomic unit on the Kubernetes platform. When we create a Deployment on Kubernetes, that Deployment creates Pods with containers inside them (as opposed to creating containers directly). Each Pod is tied to the Node where it is scheduled, and remains there until termination (according to restart policy) or deletion. In case of a Node failure, identical Pods are scheduled on other available Nodes in the cluster.

### nodes

A Pod always runs on a Node. A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster. Each Node is managed by the Master. A Node can have multiple pods, and the Kubernetes master automatically handles scheduling the pods across the Nodes in the cluster. The Master's automatic scheduling takes into account the available resources on each Node.

Every Kubernetes Node runs at least:

- Kubelet, a process responsible for communication between the Kubernetes Master and the Node; it manages the Pods and the containers running on a machine.
- A container runtime (like Docker, rkt) responsible for pulling the container image from a registry, unpacking the container, and running the application.

The most common operations can be done with the following kubectl commands:

```
kubectl get - list resources
kubectl describe - show detailed information about a resource
kubectl logs - print the logs from a container in a pod
kubectl exec - execute a command on a container in a pod
```

You can use these commands to see when applications were deployed, what their current statuses are, where they are running and what their configurations are.

这些命令可以查看应用发布时间，当前状态，在哪些机器上跑，配置项

### troubleshooting

#### 查看pods

We’ll use the kubectl get command and look for existing Pods:

```sh
$ kubectl get pods
NAME                                   READY   STATUS    RESTARTS AGE
kubernetes-bootcamp-765bf4c7b4-z4s2w   1/1     Running   0 45s
$
```

#### 查看pods 详细信息(containers, images...)

to view what containers are inside that Pod and what images are used to build those containers we run the describe pods command:

```sh
$ kubectl describe pods
Name:         kubernetes-bootcamp-765bf4c7b4-z4s2w
Namespace:    default
Priority:     0
Node:         minikube/172.17.0.20
Start Time:   Sun, 28 Jun 2020 08:09:36 +0000
Labels:       pod-template-hash=765bf4c7b4
              run=kubernetes-bootcamp
Annotations:  <none>
Status:       Running
IP:           172.18.0.4
IPs:
  IP:           172.18.0.4
Controlled By:  ReplicaSet/kubernetes-bootcamp-765bf4c7b4
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://a76a24106ca635211fd9396a2dc9ea5e16bd575fa783ddbe3d04bc34a234a4d8
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://jocatalin/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 28 J
```

We see here details about the Pod’s container: IP address, the ports used and a list of events related to the lifecycle of the Pod.
能看到ip, 端口，一些生命周期相关的events

the describe command can be used to get detailed information about most of the kubernetes primitives: node, pods, deployments. The describe output is designed to be human readable, not to be scripted against.
describe 能查看node, pods, deployment

#### logs

Anything that the application would normally send to STDOUT becomes logs for the container within the Pod. We can retrieve these logs using the kubectl logs command:

We don’t need to specify the container name, because we only have one container inside the pod.

#### exec

We can execute commands directly on the container once the Pod is up and running.
list the environment variables:

```sh
kubectl exec $POD_NAME env

$ kubectl exec mng-api2-cv-757979b5bf-9dp72 pwd
/
$ kubectl exec mng-api2-cv-757979b5bf-9dp72 whoami
root
$
```

##### 打开一个bash

start a bash session in the Pod’s container:

```sh
kubectl exec -ti $POD_NAME bash
```
