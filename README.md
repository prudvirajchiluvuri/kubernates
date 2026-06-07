lHere is your content converted into clean, structured **Markdown format**:

---

# Kubernetes

* The word **Kubernetes** originated from Greek, meaning **"pilot"**.
* It is also called **K8s**.
* It was originally developed by Google.

---

## Why Containers Are Needed in Production

* Containers are needed in production environments.
* If one container stops, wouldn’t it be easier to automatically start another one?
* Kubernetes helps with:

  * Scaling
  * Failover
  * High availability

---

# Features of Kubernetes

1. **Service Discovery and Load Balancing**

   * Distributes network traffic across containers.

2. **Storage Orchestration**

   * Allows storage from:

     * Local storage
     * Cloud providers
     * Network storage systems

3. **Automated Rollouts and Rollbacks**

   * Automatically updates applications.
   * Rolls back if something fails.

4. **Automatic Bin Packing**

   * Efficiently places containers based on resource requirements.

5. **Self-Healing**

   * Restarts failed containers.
   * Replaces unhealthy Pods.

6. **Secret and Configuration Management**

   * Manages sensitive information securely.

7. **Batch Execution**

   * Runs batch jobs and scheduled tasks.

8. **Horizontal Scaling**

   * Scale applications up or down automatically.

9. **IPv4/IPv6 Dual Stack Support**

   * Supports both IPv4 and IPv6 for Pods and Services.

---

# Kubernetes Architecture Components

## Control Plane Components

### API Server

* Serves the Kubernetes APIs.
* Entry point for all cluster communication.

### etcd

* Key-value store.
* Stores cluster data processed by the API server.

### kube-controller-manager

* Ensures the current state matches the desired state.

### Scheduler

* Schedules Pods to run on nodes.

### Cloud Controller Manager

* Connects Kubernetes with external cloud providers.

---

# Node Components

* A **Node** is a machine (physical or virtual).

## Pods

* A **Pod** is a group of containers.
* Containers inside a Pod:

  * Share the same network
  * Share storage

---

# Command-Line and Networking Components

### kubectl

* Used to control and manage the cluster.
* Sends commands to the API server.

### kube-proxy

* Handles networking rules on nodes.
* Acts like a network traffic manager (not exactly a firewall).

---

# Creating Objects in Kubernetes

We create objects in Kubernetes using a **YAML (.yml) file**.

For example, we create a **Deployment** object.

In the Deployment file, we specify the `spec` field.  
Inside `spec`, we define the **desired state** of the application (such as the number of replicas, container image, etc.).

The Kubernetes controller continuously checks the current state of the cluster and compares it with the desired state defined in the `spec`.

If there is any difference, the controller updates the system to match the desired state you provided.



# Kubernetes Object Structure

Each Kubernetes object has a different `spec` depending on its type.

## apiVersion
Defines which API version of the Kubernetes API server you are using for this object.

Example:
- `v1`
- `apps/v1`
- `batch/v1`

## kind
Specifies the type of object you are creating.

Examples:
- Pod
- Deployment
- Service
- Job

## metadata
Contains data that helps uniquely identify the object, such as:
- `name` – Name of the object
- `namespace` – The namespace where it exists
- `uid` – A unique identifier automatically assigned by Kubernetes
- `labels` – Key-value pairs for organizing objects

## spec
Stands for specification.

This is where you define the desired state of the object.
Each object type has a different `spec` structure.

# Kubernetes Server-Side Field Validation (v1.25+)

Starting with **Kubernetes v1.25**, the API server can perform **server-side field validation**, which detects:

- Unrecognized fields in an object  
- Duplicate fields in an object  

This provides the same functionality as `kubectl --validate`, but on the **server side**.

---

## kubectl `--validate` flag

The `--validate` flag controls the **level of field validation**. It accepts:

| Value   | Behavior |
|---------|----------|
| `strict` | Performs strict validation. Errors will **fail the request**. |
| `warn`   | Validation is performed, but errors show as **warnings**, not failures. |
| `ignore` | No server-side validation is performed. |
| `true`  | Equivalent to `strict`. |
| `false` | Equivalent to `ignore`. |

**Default:** `--validate=true` (strict validation)

---

### Summary

- **Strict:** Stop on validation errors.  
- **Warn:** Continue but show warnings.  
- **Ignore:** Skip validation entirely.  
# Kubernetes Object Management - Summary

## Overview
This document covers different approaches to creating and managing Kubernetes objects using the `kubectl` command-line tool.

## Three Management Techniques

### 1. Imperative Commands
**Best for:** Development projects, one-off tasks

**How it works:** Operate directly on live objects in the cluster using kubectl commands

**Example:**
```bash
kubectl create deployment nginx --image nginx
```

**Pros:**
- Simple single-action commands
- Quick to execute (one step)
- Lowest learning curve

**Cons:**
- No change review process integration
- No audit trail
- No configuration history
- No template for reuse

---

### 2. Imperative Object Configuration
**Best for:** Production projects with single maintainer

**How it works:** Specify operations and configuration files (YAML/JSON) with complete object definitions

**Examples:**
```bash
# Create objects from file
kubectl create -f nginx.yaml

# Delete objects
kubectl delete -f nginx.yaml -f redis.yaml

# Update/replace objects
kubectl replace -f nginx.yaml
```

**Pros:**
- Configuration stored in version control (Git)
- Integrates with review processes
- Provides audit trail
- Serves as template for new objects

**Cons:**
- Works best on files, not directories

---

### 3. Declarative Object Configuration
**Best for:** Production projects with multiple contributors

**How it works:** kubectl automatically detects create/update/delete operations based on configuration files

**Examples:**
```bash
# Preview changes
kubectl diff -f configs/

# Apply changes
kubectl apply -f configs/

# Recursively process directories
kubectl apply -R -f configs/
```

**Pros:**
- Retains changes made by other writers
- Better support for directories
- Auto-detects operation types per object
- Preserves manual changes to live objects

**Cons:**
- Harder to debug
- More complex merge/patch operations
- Steepest learning curve

---
# Kubernetes Object Naming Rules

## 🔹 Uniqueness
- **Object name** must be unique within a **specific resource type**.
- Example:
  - `my-app1` can be a **Pod**
  - `my-app1` can also be a **Deployment**
- These do not conflict because they are different resource types.

- **UUID**:
  - Always unique across the **entire cluster**

---

## 🔹 API Version
- `apiVersion` is **irrelevant** to object naming
- Naming rules apply **regardless of API version**

---

## 🔹 generateName
- Instead of `name`, you can use `generateName`
- It acts as a **prefix**
- Kubernetes will add a **random suffix**

### Example:
```yaml
metadata:
  generateName: my-app-
```

# Kubernetes Labels

## 🔹 What are Labels?

Labels are **key/value pairs** attached to Kubernetes objects during creation and can also be modified later.

* Help in **querying and filtering objects**
* Can be **shared across multiple objects** (not unique)

### Example

```yaml
metadata:
  labels:
    key1: value1
```

---

## 🔹 Why Labels?

Rigid folder structures are difficult to query.

### Example:

```
/production
  /frontend
    /v1
```

➡️ Querying becomes complex.

👉 Kubernetes uses **labels instead of folder structures** for better flexibility and querying.

---

## 🔹 Label Structure

A label has **2 parts**:

```
prefix/name = value
```

* **prefix** → Optional
* **name** → Required

### Purpose of Prefix

* Used by system components to differentiate from user-defined labels

### Example

```
kubernetes.io/hostname=pod1
```

👉 Without prefix (e.g., `hostname`), it's hard to identify whether it is system-defined or user-defined.

### Reserved Prefixes

* `kubernetes.io/`
* `k8s.io/`

---

## 🔹 Label Selectors

There are **2 types of selectors**:

### 1) Equality-Based Selectors

Operators:

* `=`
* `==`
* `!=`

👉 Multiple conditions can be combined using commas.

#### Example

```
environment=production,tier=frontend
```

---

### 2) Set-Based Selectors

Operators:

* `in`
* `notin`
* `exists`

👉 Can be combined and mixed with equality-based selectors.

#### Examples

```
environment in (production, qa)
tier notin (frontend, backend)
```

* `notin` also matches objects **without the label**

```
partition
```

* Matches objects **that have the label** (value not checked)

```
!partition
```

* Matches objects **without the label**

---

## 🔹 Query Parameters (API Filtering)

### Equality-Based

```
?labelSelector=environment%3Dproduction,tier%3Dfrontend
```

### Set-Based

```
?labelSelector=environment+in+%28production%2Cqa%29%2Ctier+in+%28frontend%29
```

---

## 🔹 Selectors in YAML

```yaml
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - key: tier
      operator: In
      values:
        - cache
    - key: environment
      operator: NotIn
      values:
        - dev
```

* **matchLabels** → Simple key=value matching
* **matchExpressions** → Advanced filtering using operators

---

## 🔹 Updating Labels

### Example:

Find pods with `app=nginx` and add `tier=fe`

```bash
kubectl label pods -l app=nginx tier=fe
```

# Kubernetes Metadata

Kubernetes objects support **metadata**, which helps in organizing, managing, and adding additional information.

---

## 🔹 Types of Metadata

### 1) Labels

- Used for **selecting and filtering objects**
- Helps in grouping and querying resources



### 2) Annotations

- Used to **store additional information**
- Not used for filtering like labels
- Useful for **documentation, tracking, and integrations**



## 🔹 Annotation Use Cases

Annotations can store various types of information:



### 📌 1) Build / Release / Image Information

- Build timestamp  
- Release version (e.g., `main`, `v1.0`)  
- Docker image details  



### 📌 2) External Tool Links

- Logging tools  
- Monitoring systems  
- Analytics dashboards  



### 📌 3) CI/CD Pipeline Links

- Pipeline execution links  
- Deployment jobs  
- Build system references  



### 📌 4) Rollout Metadata

- Deployment configurations  
- Checkpoints  
- Version tracking  


### 📌 5) Contact Information

- Owner details  
- Team contact  
- Support or escalation info  


## 🔹 Example

```yaml
metadata:
  labels:
    app: nginx
  annotations:
    build: "2026-03-22T10:00:00Z"
    release: "main"
    image: "nginx:latest"
    monitoring: "https://grafana.example.com/dashboard"
    cicd: "https://jenkins.example.com/job/123"
    owner: "team@example.com"
```
# Kubernetes Field Selectors

Field selectors are used for **filtering Kubernetes objects based on object properties (fields)**.



## 🔹 What are Field Selectors?

* Field selectors are used for **filtering resources**
* They use **object properties (fields)** instead of labels
* These fields are **predefined by Kubernetes**



## 🔹 Examples

```bash
metadata.name=my-service
metadata.namespace!=default
status.phase=Pending
```


## 🔹 Important Notes

* Field selector properties **vary based on resource type**

  * Different resources (Pods, Services, StatefulSets, etc.) support different fields

## 🔹 Supported Operators

Field selectors support only **equality-based operators**:

* `=`
* `==`
* `!=`

❌ Not supported:

* `in`
* `notin`
* Any set-based operators


## 🔹 Multiple Conditions

You can combine multiple conditions using commas:

```bash
kubectl get pods --field-selector=status.phase!=Running,spec.restartPolicy=Always
```


## 🔹 Multiple Resource Types

You can query multiple resource types in a single command:

```bash
kubectl get statefulsets,services --all-namespaces --field-selector=metadata.namespace!=default
```
---

# Kubernetes Finalizers & Owner References (Simple Notes)


## 🔹 Finalizers

Finalizers are **keys (strings)** stored in:

```yaml
metadata.finalizers
```

👉 They tell Kubernetes:

> Wait before deleting this object until some condition is met.



## 🔹 How Deletion Works

When you try to delete an object:

```bash
kubectl delete <resource>
```

Kubernetes does the following:

1. Sets:

   ```yaml
   metadata.deletionTimestamp
   ```

2. API returns:

   ```
   202 Accepted
   ```

3. Object goes into:

   ```
   Terminating
   ```



## 🔹 When is Object Deleted?

👉 Object is deleted ONLY when:

```yaml
metadata.finalizers: []
```



## 🔹 Example: PV Protection

```yaml
finalizers:
  - kubernetes.io/pv-protection
```

### Behavior:

* Added automatically by Kubernetes
* Prevents deletion of PersistentVolume

### When Added?

* When a Pod is using the storage

### When Removed?

* When the storage is no longer in use



## 🔹 Owner References (Parent → Child)

Example:

* Job → Parent
* Pod → Child

Pod contains:

```yaml
ownerReferences:
```

👉 This tells Kubernetes that:

> Pod belongs to Job



## 🔹 Deletion Flow Example

### Step 1: Create Job

* Job creates Pods
* Pods have ownerReferences



### Step 2: Delete Job

```bash
kubectl delete job my-job
```



### Step 3: Kubernetes Behavior

* Finds Pods using ownerReferences
* Tries to delete Pods first



### Step 4: Pod Has Finalizer

```yaml
finalizers:
  - example.com/cleanup
```

👉 Then:

* Pod deletion is blocked
* Pod stays in Terminating state



### Step 5: Impact on Job

* Pods are not deleted
* Job deletion is delayed



## 🔹 Key Concept

* Finalizer on child can block parent deletion indirectly


## 🔹 Simple Summary

* Finalizers → Delay deletion until safe
* OwnerReferences → Define parent-child relationship
* Object is deleted only when finalizers list is empty
* Child finalizer can delay parent deletion


This field decides whether the parent object can be garbage collected.

👉 It is **by default set to `true`**.  
👉 If needed, you can manually set it to `false`.


## 🔹 Behavior

- **`blockOwnerDeletion: true`**
  - Prevents the parent object from being deleted.
  - The child object can block garbage collection of the parent.

- **`blockOwnerDeletion: false`**
  - Allows the parent object to be deleted.
  - The child object does not block parent deletion.



## 🔹 Summary

| Value  | Behavior |
|--------|---------|
| true   | Prevents parent deletion |
| false  | Allows parent deletion |

---
# Kubernetes Containers & Runtime Notes

## 📦 Container Basics

- A container is a package of:
  - Application
  - Dependencies
  - System libraries

- Containers are **decoupled from underlying infrastructure**, so they can run on any OS.

---

## ⚙️ Container Runtimes

Kubernetes supports:
- containerd
- CRI-O
- Any implementation of **CRI (Container Runtime Interface)**

---

## 🖼️ Container Image Formats

### 1. Using Tag
repo/name:tag

- Tag can change over time ❌
- Not reliable for production

---

### 2. Using Digest
repo/name@sha256:2343...

- Digest is fixed ✅
- Immutable and unique

👉 **Best Practice:** Always use digest instead of tag

---

## 🌐 Image Registry with Port

Example:
fictional.registry.example:10443/imagename

- Used when registry runs on a **custom port**

---

## 📥 Image Pull Policy

### 1. IfNotPresent
- Use local image if available
- Otherwise pull from registry

### 2. Always
- Always check and pull from registry

### 3. Never
- Use only local image
- Fail if not available

---

## ⚠️ Default Image Pull Policy Behavior

| Condition | Policy |
|----------|--------|
| No tag or `:latest` | Always |
| Tag exists (not latest) | IfNotPresent |
| Digest used | IfNotPresent |

---

## 🔐 Pulling Images from Private Registry

### 1. Using imagePullSecrets (Recommended)

- Secret must be in same namespace

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: mycompany/private-app
  imagePullSecrets:
  - name: my-secret
```

---

### 2. Node-Level Authentication (`docker login`)

#### Example config.json

```json
{
  "auths": {
    "my-registry.example/images": {
      "auth": "user1:pass1"
    },
    "*.my-registry.example/images/subpath": {
      "auth": "user2:pass2"
    }
  }
}
```

---

### 3. Static Pods + Credential Plugin

- Managed by kubelet directly
- Do NOT use Secrets or ServiceAccounts
- Plugin fetches credentials

---

### 4. Pre-pulled Images

- Images already present on node
- Requires same images on all nodes

---

## 🌱 Container Environment

- Pod name & namespace injected as env variables
- Services in same namespace also injected

---

## 🔄 Container Lifecycle Hooks

### Hooks:
- PostStart
- PreStop
- StopSignal

### Handlers:
- Exec (inside container)
- HTTP (kubelet)
- Sleep (delay)

---

## ⚠️ Important Notes

- PostStart runs parallel to container start
- PreStop must finish before SIGTERM
- Grace period includes PreStop + shutdown time
- Hooks may run multiple times
- Debug using:
  kubectl describe pod

---

## 🧠 Key Takeaways

- Use digest over tags
- Prefer imagePullSecrets
- Avoid heavy PostStart logic
- Ensure PreStop fits in grace period


----------------------------------
# Kubernetes Pods and PodTemplate Notes

Pods are a group of containers that share the same network and storage resources.

Usually, containers inside a Pod work closely together.  
Example:
- Main application container
- Logging/monitoring sidecar container

## A Pod can contain

### Init Containers
Used for initialization tasks before the main container starts.

### Main Containers
These run the actual application.

### Ephemeral Containers
Temporary containers mainly used for debugging.  
They are not part of the normal application lifecycle.

Conceptually:

```text
Pod = Init Containers + Main Containers + Ephemeral Containers
```

However, in most real-world applications, people usually prefer:
- one main application container per Pod
- plus optional helper/sidecar containers



# Example of a Simple Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  containers:
    - name: linux2
      image: nginx
```

Usually, Pods are not created directly.  
Instead, they are managed by workload resources such as:

- Kubernetes Deployment
- Kubernetes Job
- Kubernetes StatefulSet
- Kubernetes DaemonSet

`StatefulSet` is mainly used when applications need stable identity and state tracking.

If you need multiple replicas of Pods, you should not manually create multiple Pods.  
Instead, workload resources handle replication and management automatically.

The controller continuously monitors the Pods and maintains the desired state.


# spec.os.name

`spec.os.name` specifies the intended operating system for the Pod.

Example:

```yaml
spec:
  os:
    name: linux
```

However, in current Kubernetes versions, this field alone does not guarantee scheduling to the correct node.

To actually place the Pod on the correct OS node, you should use:

```yaml
nodeSelector:
  kubernetes.io/os: linux
```


# Auto-Healing

If a Pod fails or a node becomes unhealthy, the controller creates a replacement Pod on a healthy node.



# PodTemplate

A `PodTemplate` is a specification used by workload resources to create Pods.

Example:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello
spec:
  template:
    spec:
      containers:
        - name: hello
          image: busybox:1.28
          command:
            - sh
            - -c
            - echo "Hello, Kubernetes!" && sleep 3600
      restartPolicy: OnFailure
```

The `template` section is the PodTemplate.

Controllers use this template to:
- create Pods
- replace failed Pods
- scale Pods
- update Pods

When the PodTemplate changes, the controller gradually replaces old Pods with new Pods containing the updated configuration.



# Pod Networking and Storage

All containers inside the same Pod:
- share the same IP address
- share the same network namespace
- can communicate using `localhost`
- share the same storage volumes

Containers inside the same Pod are tightly coupled and designed to work together.
<img width="1536" height="1024" alt="ChatGPT Image May 24, 2026, 12_34_33 AM" src="https://github.com/user-attachments/assets/1196335a-e74b-47d6-bd51-c3778765167e" />


# Kubernetes Resource Requests and Limits

Kubernetes allows you to define how much CPU and memory a container needs.

There are two important concepts:

| Concept | Meaning |
|---|---|
| Requests | Minimum resources guaranteed |
| Limits | Maximum resources allowed |


# Kubernetes Example

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "500m"

  limits:
    memory: "512Mi"
    cpu: "1"
```

# What This Means

## Requests

```yaml
requests:
```

Container needs at least:
- 256MB RAM
- 0.5 CPU

The Kubernetes scheduler uses this information to decide where to place the Pod.

If a node does not have enough free resources:
- Pod stays in Pending state



# Limits

```yaml
limits:
```

Container cannot exceed:
- 512MB RAM
- 1 CPU


# CPU Limit Behavior

If the container tries to use too much CPU:

```text
Kubernetes slows it down
```

This is called:

```text
CPU Throttling
```

The container continues running but becomes slower.


# Memory Limit Behavior

If the container exceeds memory:

```text
Kernel kills the container
```

This is called:

```text
OOM Kill (Out Of Memory)
```


# Important Difference

| Resource | If Limit Exceeded |
|---|---|
| CPU | Slowed down |
| Memory | Container killed |


# Why Limits Exist

Suppose one container uses all CPU or memory.

Other applications on the same node become slow.

This problem is called:

```text
Noisy Neighbor Problem
```

Limits help prevent this.


# CPU Limits Downside

Suppose your node has:

```text
8 CPUs total
```

Your container limit is:

```yaml
limits:
  cpu: 1
```

Meaning:

```text
Container can use maximum 1 CPU only
```

# Important Scenario

Node usage:

```text
Only 2 CPUs are being used
6 CPUs are FREE
```

But your application suddenly needs:

```text
2 CPUs temporarily
```

Kubernetes says:

```text
NO
Your limit is 1 CPU
```

Even though:
- machine has free CPU
- nobody else is using it

your container still gets restricted.

This restriction is called:

```text
CPU Throttling
```

# Result

Your application becomes:
- slower
- delayed
- high latency

Especially for:
- APIs
- Real-time apps
- Gaming systems
- Trading systems


# Why Memory Limits Are Different

Memory cannot be shared safely like CPU.

If one application uses too much RAM:
- node may crash
- other applications may fail

So memory limits are very important.

CPU is more flexible because workloads can share CPU time.

That is why some companies:
- always set memory limits
- sometimes avoid strict CPU limits

especially for performance-sensitive applications.

# Init Container vs Sidecar Container

Earlier in Kubernetes:

```text
Init Container → starts → completes work → exits
```

Init containers were temporary setup containers.


# New Sidecar Feature

Now Kubernetes allows:

```yaml
restartPolicy: Always
```

inside an init container.

This makes the init container behave like a sidecar container.


# What Happens

```text
1. Sidecar starts first
2. Main application starts
3. Both keep running together
4. Both stop when Pod stops
```

# Can Init Container Become Sidecar?

YES ✅

If an init container uses:

```yaml
restartPolicy: Always
```

it becomes a long-running sidecar-style container.


# Can We Have Multiple Sidecars?

YES ✅

A Pod can contain:

```text
Main App Container
+
Logging Sidecar
+
Monitoring Sidecar
+
Proxy Sidecar
```

# Static Pods

Static Pods are managed directly by the kubelet daemon on a specific node, without the API server observing them. Whereas most Pods are managed by the control plane (for example, a Deployment), static Pods are directly supervised by the kubelet, which also restarts them if they fail.

## Key Points

- Static Pods are managed directly by the kubelet on a specific node.
- They are **not managed by the Kubernetes control plane**.
- The kubelet ensures the Pod is running and restarts it if it fails.
- Each static Pod is bound to a single node.

## Use Case

The main use of static Pods is to run a **self-hosted control plane**. In this setup, the kubelet supervises critical control plane components directly.

## Mirror Pods

- The kubelet creates a *mirror Pod* in the Kubernetes API server for each static Pod.
- This makes static Pods visible via the API server.
- However, they **cannot be managed or modified via the API server**.

## Important Note

> The spec of a static Pod cannot refer to other API objects such as:
> - ServiceAccount  
> - ConfigMap  
> - Secret  
> - Any other Kubernetes API resources

## Summary

Static Pods are directly managed by the kubelet, run on a single node, and are mainly used for control plane components in self-hosted Kubernetes setups.

# Kubernetes Pod Lifecycle & Restart Notes

# Pod Lifecycle

* `kubectl` itself does **not** restart Pods.
* The **kubelet** and higher-level controllers (Deployment, ReplicaSet, StatefulSet, etc.) are responsible for restarting or recreating Pods.
* If a **node fails**, the Pod running on that node cannot continue there.
* If the Pod is managed by a controller, Kubernetes schedules a **new Pod** on another healthy node.


# Scheduling vs Binding

## Scheduling

Scheduling is the process of **deciding which node a Pod should run on**.

```
Pod
   ↓
Scheduler selects Node
```



## Binding

Binding is the process of **assigning the selected Pod to the chosen node**.

```
Scheduler chooses Node
          ↓
Pod is bound to Node
```



# Pod Deletion and Volumes

* When a Pod is deleted, **ephemeral volumes** (such as `emptyDir`) are deleted.
* **PersistentVolumes (PV/PVC)** are **not automatically deleted** simply because the Pod is deleted.


# Pod Phase (`status.phase`)

## Pending

* Kubernetes accepted the Pod.
* Waiting for scheduling, image pulling, or resource allocation.


## Running

* Pod has been assigned to a node.
* At least one container is running.


## Succeeded

* All containers terminated successfully.
* Exit code = 0.



## Failed

* At least one container terminated with failure.
* Pod will not continue successfully.



## Unknown

* Kubernetes cannot determine the Pod state.
* Usually due to communication problems with the node.


## Terminating

Occurs when a Pod is being deleted.


# CrashLoopBackOff

CrashLoopBackOff means:

> The container starts, crashes, restarts repeatedly, and Kubernetes waits longer between restart attempts.

Example:

```
Start
  ↓
Crash
  ↓
Restart
  ↓
Crash
  ↓
Wait (Backoff)
  ↓
Restart
```


## Backoff Reset

If the container runs successfully for approximately **10 minutes**, Kubernetes resets the exponential backoff timer and treats the next crash as a first failure.


# Container States

A container has three states:

## Waiting

Examples:

* Pulling image
* Creating container
* Mounting volumes
* Configuring secrets



## Running

Container is executing successfully.


## Terminated

Container execution finished.

Termination may be:

* Successful
* Failed

Check the termination reason for details.



# Common Reasons for Pod Crashes

1. Application bugs
2. Incorrect configuration

   * Environment variables
   * Mounted volumes
3. Insufficient CPU or memory
4. Health probe failures



# Investigating Pod Failures

Useful commands:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl get events
```


# Pod-Level Restart Policy

Pod restart policies:

* Always
* OnFailure
* Never

These apply to:

* App containers
* Init containers

Sidecar containers can have their own restart behavior and may not always follow the Pod-level policy depending on the feature being used.



# Container-Level Restart Policy

Requirements:

* `ContainerRestartRules` feature gate enabled.

Then individual containers can specify:

```yaml
restartPolicy:
restartPolicyRules:
```

to override the Pod policy.

Applicable to:

* App containers
* Init containers


## Example

```yaml
restartPolicy: Never

containers:
- name: app
  restartPolicy: Never
  restartPolicyRules:
  - action: Restart
    exitCodes:
      operator: In
      values: [42]
```

Only exit code **42** causes restart.



# Restart Entire Pod In Place

Requirement:

* `RestartAllContainersOnContainerExits` feature gate enabled.

Use:

```yaml
action: RestartAllContainers
```

inside `restartPolicyRules`.

When matched:

* Entire Pod restarts **in place**.



## In-place Restart Preserves

* Pod name
* Pod IP
* Volumes
* Network identity



## In-place Restart Flow

```
Container exits
      ↓
RestartAllContainers triggered
      ↓
All containers terminated
      ↓
Init containers start again
      ↓
Main containers start
      ↓
Sidecars start
```


## PodRestartInPlace Flag

During restart:

```
PodRestartInPlace=True
```

After restart completes:

```
PodRestartInPlace=False
```


# Pod Conditions

`status.phase` tells whether a Pod is Running, Failed, etc.

Pod Conditions provide more detailed lifecycle information.



## PodCondition Structure

* type
* status
* lastProbeTime
* lastTransitionTime
* reason
* message
* observedGeneration



# Built-in Pod Conditions

## PodScheduled

Pod has been assigned to a node.


## PodReadyToStartContainers

Sandbox and networking are ready.

CRI has:

* Created sandbox
* Configured networking

Only then can image pulling and container creation begin.



## Initialized

All init containers completed successfully.

If there are no init containers, this becomes true immediately.



## ContainersReady

All containers are ready.


## Ready

Pod is ready to serve traffic.


# PodReadyToStartContainers = False

Occurs when:

1. Sandbox has not yet been created.

OR

2. Sandbox disappeared because:

* Node rebooted
* Pod sandbox VM rebooted


# Other Pod Conditions

* PodResizePending
* PodResizeInProgress
* DisruptionTarget



# DisruptionTarget

Indicates Kubernetes plans to terminate the Pod because of an external disruption.

## Reasons

### PreemptionByScheduler

Higher-priority Pod needs resources.


### DeletionByTaintManager

Node has a `NoExecute` taint that the Pod does not tolerate.



### EvictionByEvictionAPI

Pod eviction requested via Kubernetes API.



### DeletionByPodGC

Node no longer exists and Pod is garbage collected.



### TerminationByKubelet

Examples:

* Node memory pressure
* Disk pressure
* Graceful node shutdown
* System-critical Pod preemption


# Pod Resize Conditions

## PodResizePending

Resize requested but cannot yet be granted.

Reason `Infeasible` means requested size cannot be satisfied.



## PodResizeInProgress

Resize has been accepted and is currently being applied.



# Init Containers

Init containers:

* Run before application containers.
* Run sequentially.
* Next init container starts only after previous one succeeds.

```
Init1
   ↓
Init2
   ↓
Init3
   ↓
Main Container
```


## If Init Container Fails

It is restarted according to restart policy.

If restart policy is `Never` and it fails:

```
Pod → Failed
```

Status:

```
.status.initContainerStatuses
```


## Init Container Supports

* Volumes
* Security settings
* Resource limits

Init containers **do not support**:

* Lifecycle hooks
* Liveness probes
* Readiness probes
* Startup probes



# Sidecar Containers

Sidecar containers:

* Start before or alongside the main application depending on configuration.
* Continue running during the Pod lifetime.

Unlike init containers, sidecars support:

* Lifecycle hooks
* Liveness probes
* Readiness probes
* Startup probes


# Difference Between Init Container and Sidecar

| Init Container                                                         | Sidecar Container                                                 |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------- |
| Runs before main container                                             | Runs alongside the main container                                 |
| Executes once                                                          | Continues running                                                 |
| Must finish before app starts                                          | Provides supporting functionality throughout Pod lifetime         |
| Runs sequentially if multiple exist                                    | Runs concurrently with the application                            |
| Does **not** support lifecycle, liveness, readiness, or startup probes | Supports lifecycle hooks, liveness, readiness, and startup probes |
# Kubernetes Init Containers

## What is an Init Container?

* An **init container** runs **before** the main application containers.
* All init containers must **complete successfully** before the app containers start.
* They run **only once per Pod startup**.



# Uses of Init Containers

## 1. Install or prepare tools required by the main container

* Download binaries
* Install utilities
* Generate configuration files

Example:

* Init container downloads a migration script.
* Main application uses it after startup.



## 2. Reduce the attack surface

Instead of building one large image containing:

* Application
* curl
* wget
* git
* bash
* debugging tools

You can:

* Keep the main application image minimal.
* Use an init container for temporary setup tasks.

**Benefit:**

* Smaller image
* Better security
* Fewer vulnerabilities



## 3. Split responsibilities instead of one big image

Rather than creating a single image that performs:

* Setup
* Configuration
* Application startup

You can separate them:

```
Init Container
    ↓
Performs setup

Main Container
    ↓
Runs the application
```

This follows the principle of one responsibility per container.


## 4. Ensure preconditions are met before starting the application

The main container starts **only after** all init containers finish successfully.

Examples:

* Wait for a database to become available
* Wait for another service to be reachable
* Generate required configuration
* Download required files

If an init container fails, the main container will **not** start.



# Common Use Cases

## 1. Wait until a service is up

Example:

```
Init Container
      ↓
Checks Database
      ↓
Database Ready?
      ↓
Yes
      ↓
Main Application Starts
```


## 2. Wait for dependencies

Examples:

* Database
* Redis
* Kafka
* External API

The application starts only after dependencies are available.


## 3. Clone a repository into a shared volume

Example:

```
Git Repository
        ↓
Init Container
        ↓
Shared Volume
        ↓
Main Container
```

The main application reads the files from the shared volume.



## 4. Inject Pod information

The init container can collect information such as:

* Pod name
* IP address
* Namespace
* Configuration

It can write this information into a shared volume for the main container to consume.


# Restart Behavior

If the Pod is recreated or restarted from scratch:

```
Init Container
      ↓
Runs Again
      ↓
Main Container Starts
```

Therefore, init containers should be **idempotent**.

## Idempotent means

Running the same operation multiple times should produce the same correct result.

Example:

Good:

```
Create directory if it does not exist
```

Bad:

```
Insert duplicate data every time it runs
```

# Updating Init Containers

Changing the init container specification or image **does not affect an already running Pod**.

The changes take effect **only when a new Pod is created**, such as during:

* Deployment rollout
* Pod deletion and recreation
* Scaling operations


# Pod Template Changes

If you modify the Pod template in a Deployment, Kubernetes creates **new Pods** with the updated template.

The impact depends on the controller:

* Deployment → Rolling update
* Job → New execution
* StatefulSet → Ordered updates (depending on strategy)


# Readiness Probe

Init containers **cannot have a `readinessProbe`**.

Reason:

* They either:

  * are running, or
  * have completed.

They do not have a separate "Ready" state like long-running application containers.


# activeDeadlineSeconds

`activeDeadlineSeconds` can be set on a Pod to prevent it from retrying forever.

Example:

```
Pod starts
      ↓
Init container keeps failing
      ↓
Deadline exceeded
      ↓
Pod is terminated
```

**Note:**
The deadline includes both:

* Init container execution time
* Main container execution time

Because of this, it is generally more suitable for **Jobs** than for long-running applications.



# Important Points

* Init containers run before application containers.
* All init containers must complete successfully.
* Main containers do not start until init containers finish.
* Init containers cannot have a `readinessProbe`.
* They should be **idempotent** because they may run again when a new Pod is created.
* Updating an init container affects only newly created Pods.
* `activeDeadlineSeconds` limits the total Pod lifetime, including init containers.

# Ephemeral Containers (Simple Explanation)

## What is an Ephemeral Container?

An **ephemeral container** is a **temporary container** that is added to a **running Pod** for **debugging or troubleshooting**.

It is **not part of the original application** and is **not meant to run permanently**.



# Why do we need it?

Suppose you have a Pod:

```
Pod
│
└── App Container
      └── Java Application
```

Your application is having issues.

You try:

```bash
kubectl exec -it my-pod -- bash
```

But the container is built from a minimal image and doesn't have:

- bash
- curl
- ping
- ps
- netstat

So you cannot debug it.


# Solution: Ephemeral Container

Kubernetes allows you to add a temporary debugging container.

Before:

```
Pod
│
└── App Container
```

After:

```
Pod
│
├── App Container
│
└── Ephemeral Container
      (BusyBox/Ubuntu)
```

Example:

```bash
kubectl debug my-pod -it --image=busybox
```

This creates a temporary BusyBox container inside the same Pod.



# Why not create another Pod?

Because you want to inspect **the existing running Pod**.

The existing Pod may have:

- Current application state
- Running processes
- Active network connections
- Logs and temporary files

A new Pod starts fresh and won't have the same state.



# Why is it called "Ephemeral"?

**Ephemeral** means **temporary** or **short-lived**.

Example timeline:

```
09:00

Pod
│
└── App Container
```

Problem occurs.

```
09:05

Pod
│
├── App Container
└── Ephemeral Container
```

Debugging completed.

```
09:15

Pod
│
└── App Container
```

The ephemeral container is only for troubleshooting.



# What can you do inside it?

You can use debugging commands such as:

```bash
ps
```

```bash
ls
```

```bash
cat
```

```bash
curl
```

```bash
ping
```

provided those tools exist in the debugging image.


# Does it run commands inside the application container?

**No.**

It is a separate container.

Think of a Pod as a house:

```
House (Pod)

├── Room 1 → App Container
└── Room 2 → Debug Container
```

The debug container is another room in the same house.

It does **not** become the application container.

Because both are in the same Pod, they can share certain namespaces (such as the network and, when enabled, the process namespace), making debugging easier.



# kubectl exec vs kubectl debug

## kubectl exec

```
You
 │
 ▼
Existing Container
```

Runs commands inside an existing container.

Example:

```bash
kubectl exec -it my-pod -- bash
```



## kubectl debug

```
You
 │
 ▼
New Temporary Container
        │
        ▼
      Same Pod
```

Creates a new temporary debugging container.

Example:

```bash
kubectl debug my-pod -it --image=busybox
```

# Summary

| kubectl exec | kubectl debug |
|--------------|---------------|
| Uses an existing container | Creates a new temporary container |
| Runs commands inside that container | Adds a debugging container to the Pod |
| Requires debugging tools to already exist | Lets you bring your own debugging tools |
| No new container is created | A new ephemeral container is created |


# Interview Definition

> An ephemeral container is a temporary container that can be added to a running Kubernetes Pod for debugging and troubleshooting. It is not part of the application's normal workload and is not intended to run permanently.



# Easy Memory Trick

- **kubectl exec** → Go inside the existing container.
- **kubectl debug** → Bring a temporary toolbox container into the same Pod for debugging.
