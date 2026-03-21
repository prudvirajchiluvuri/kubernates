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


