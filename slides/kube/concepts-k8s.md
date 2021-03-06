# Kubernetes concepts

- Kubernetes is a container management system

- It runs and manages containerized applications on a cluster

--

- What does that really mean?

---

## Basic things we can ask Kubernetes to do

--

- Start 5 containers using image `atseashop/api:v1.3`

--

- Place an internal load balancer in front of these containers

--

- Start 10 containers using image `atseashop/webfront:v1.3`

--

- Place a public load balancer in front of these containers

--

- It's Black Friday (or Christmas), traffic spikes, grow our cluster and add containers

--

- New release! Replace my containers with the new image `atseashop/webfront:v1.4`

--

- Keep processing requests during the upgrade; update my containers one at a time

---

## Other things that Kubernetes can do for us

- Basic autoscaling

- Blue/green deployment, canary deployment

- Long running services, but also batch (one-off) jobs

- Overcommit our cluster and *evict* low-priority jobs

- Run services with *stateful* data (databases etc.)

- Fine-grained access control defining *what* can be done by *whom* on *which* resources

- Integrating third party services (*service catalog*)

- Automating complex tasks (*operators*)

---

## Kubernetes architecture

---

class: pic

![haha only kidding](images/k8s-arch1.png)

---

## Kubernetes architecture

- Ha ha ha ha

- OK, I was trying to scare you, it's much simpler than that ❤️

---

class: pic

![that one is more like the real thing](images/k8s-arch2.png)

---

## Credits

- The first schema is a Kubernetes cluster with storage backed by multi-path iSCSI

  (Courtesy of [Yongbok Kim](https://www.yongbok.net/blog/))

- The second one is a simplified representation of a Kubernetes cluster

  (Courtesy of [Imesh Gunaratne](https://medium.com/containermind/a-reference-architecture-for-deploying-wso2-middleware-on-kubernetes-d4dee7601e8e))

---

## Kubernetes architecture: the nodes

- The nodes executing our containers run a collection of services:

  - a container Engine (typically Docker)

  - kubelet (the "node agent")

  - kube-proxy (a necessary but not sufficient network component)

- Nodes were formerly called "minions"

  (You might see that word in older articles or documentation)

---

## Kubernetes architecture: the control plane

- The Kubernetes logic (its "brains") is a collection of services:

  - the API server (our point of entry to everything!)

  - core services like the scheduler and controller manager

  - `etcd` (a highly available key/value store; the "database" of Kubernetes)

- Together, these services form the control plane of our cluster

- The control plane is also called the "master"

---

## Running the control plane on special nodes

- It is common to reserve a dedicated node for the control plane

  (Except for single-node development clusters, like when using minikube)

- This node is then called a "master"

  (Yes, this is ambiguous: is the "master" a node, or the whole control plane?)

- Normal applications are restricted from running on this node

  (By using a mechanism called ["taints"](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/))

- When high availability is required, each service of the control plane must be resilient

- The control plane is then replicated on multiple nodes

  (This is sometimes called a "multi-master" setup)

---

## Running the control plane outside containers

- The services of the control plane can run in or out of containers

- For instance: since `etcd` is a critical service, some people
  deploy it directly on a dedicated cluster (without containers)

  (This is illustrated on the first "super complicated" schema)

- In some hosted Kubernetes offerings (e.g. GKE), the control plane is invisible

  (We only "see" a Kubernetes API endpoint)

- In that case, there is no "master node"

*For this reason, it is more accurate to say "control plane" rather than "master".*

---

## Do we need to run Docker at all?

No!

--

- By default, Kubernetes uses the Docker Engine to run containers

- We could also use `rkt` ("Rocket") from CoreOS

- Or leverage other pluggable runtimes through the *Container Runtime Interface*

  (like CRI-O, or containerd)

---

## Do we need to run Docker at all?

Yes!

--

- In this workshop, we run our app on a single node first

- We will need to build images and ship them around

- We can do these things without Docker
  <br/>
  (and get diagnosed with NIH¹ syndrome)

- Docker is still the most stable container engine today
  <br/>
  (but other options are maturing very quickly)

.footnote[¹[Not Invented Here](https://en.wikipedia.org/wiki/Not_invented_here)]

---

## Do we need to run Docker at all?

- On our development environments, CI pipelines ... :

  *Yes, almost certainly*

- On our production servers:

  *Yes (today)*

  *Probably not (in the future)*

.footnote[More information about CRI [on the Kubernetes blog](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes)]

---

## Kubernetes resources

- The Kubernetes API defines a lot of objects called *resources*

- These resources are organized by type, or `Kind` (in the API)

- A few common resource types are:

  - node (a machine — physical or virtual — in our cluster)
  - pod (group of containers running together on a node)
  - service (stable network endpoint to connect to one or multiple containers)
  - namespace (more-or-less isolated group of things)
  - secret (bundle of sensitive data to be passed to a container)
 
  And much more!

- We can see the full list by running `kubectl api-resources`

  (In Kubernetes 1.10 and prior, the command to list API resources was `kubectl get`)

---

class: pic

![Node, pod, container](images/k8s-arch3-thanks-weave.png)

---

class: pic

![One of the best Kubernetes architecture diagrams available](images/k8s-arch4-thanks-luxas.png)

---

## Credits

- The first diagram is courtesy of Weave Works

  - a *pod* can have multiple containers working together

  - IP addresses are associated with *pods*, not with individual containers

- The second diagram is courtesy of Lucas Käldström, in [this presentation](https://speakerdeck.com/luxas/kubeadm-cluster-creation-internals-from-self-hosting-to-upgradability-and-ha)

  - it's one of the best Kubernetes architecture diagrams available!

Both diagrams used with permission.
