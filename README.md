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


