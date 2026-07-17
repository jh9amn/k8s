# 📚 06 - Kubernetes Storage (Volumes, PV, PVC & StorageClass)

> **Containers are ephemeral, but your data shouldn't be.**
>
> Kubernetes provides different storage mechanisms to ensure application data remains available even when Pods restart, move to another node, or are recreated.

---

# 📖 Table of Contents

- Why Storage is Needed
- Ephemeral vs Persistent Storage
- Volumes
- emptyDir
- hostPath
- Persistent Volumes (PV)
- Persistent Volume Claims (PVC)
- StorageClass
- Dynamic Provisioning
- CSI (Container Storage Interface)
- Access Modes
- Reclaim Policies
- Storage Architecture
- Commands Cheat Sheet
- Troubleshooting
- Best Practices
- Interview Questions
- Quick Revision

---

# Why Do We Need Storage?

Imagine a Pod running MySQL.

```
Pod

↓

MySQL

↓

Database Files
```

A customer places an order.

```
Orders Stored
```

Now the Pod crashes.

```
Pod Deleted

↓

New Pod Created
```

Since containers are **ephemeral**, the database files inside the container disappear.

```
Customer Orders ❌ Lost
```

This is why Kubernetes separates **compute** (Pods) from **storage** (Volumes).

---

# Ephemeral vs Persistent Storage

## Ephemeral Storage

```
Pod

↓

Container Filesystem

↓

Pod Deleted

↓

Data Deleted
```

Examples:

- Cache
- Temporary files
- Session data

---

## Persistent Storage

```
Pod

↓

Persistent Volume

↓

Pod Deleted

↓

Data Still Exists ✅
```

Examples:

- MySQL
- PostgreSQL
- MongoDB
- Elasticsearch
- User uploads

---

# Kubernetes Storage Architecture

```
               Application

                    │

                    ▼

                 Pod

                    │

            Volume Mount

                    │

                    ▼

        Persistent Volume Claim

                    │

                    ▼

         Persistent Volume

                    │

                    ▼

 Storage (AWS EBS / Azure Disk /
      GCE PD / NFS / Ceph ...)
```

---

# What is a Volume?

A **Volume** is storage attached to a Pod.

Unlike the container filesystem, the Volume survives **container restarts** inside the same Pod.

```
Pod

+--------------------------+

Container

↓

Volume

+--------------------------+
```

---

# emptyDir Volume

Created when the Pod starts.

Deleted when the Pod is removed.

```
Pod Created

↓

emptyDir Created

↓

Application Uses Data

↓

Pod Deleted

↓

emptyDir Deleted
```

Example:

```yaml
volumes:
- name: cache
  emptyDir: {}

containers:
- name: app
  volumeMounts:
  - name: cache
    mountPath: /cache
```

Use cases:

- Cache
- Temporary files
- Scratch space

---

# hostPath Volume

Mounts a directory from the Node.

```
Worker Node

/var/data

↓

Mounted

↓

Pod
```

Example:

```yaml
hostPath:
  path: /data
```

⚠️ Not recommended for production because Pods scheduled on another node won't see the same data.

---

# Persistent Volume (PV)

A **Persistent Volume** is a cluster-wide storage resource.

It exists independently of Pods.

Think of it as a physical hard drive made available to Kubernetes.

```
Cluster

↓

Persistent Volume

↓

Storage
```

Example:

```yaml
apiVersion: v1
kind: PersistentVolume

spec:
  capacity:
    storage: 10Gi

  accessModes:
    - ReadWriteOnce
```

---

# Persistent Volume Claim (PVC)

Applications shouldn't know where storage comes from.

Instead, they request storage.

```
Application

↓

PVC

↓

PV
```

A PVC is a **request for storage**.

Example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 5Gi
```

---

# Relationship Between PV & PVC

```
Application

↓

Pod

↓

PVC

↓

PV

↓

Disk
```

Think of it like renting an apartment.

```
Apartment

=

Persistent Volume

Tenant

=

Persistent Volume Claim
```

The tenant requests an apartment; Kubernetes binds the PVC to a matching PV.

---

# StorageClass

Creating PVs manually doesn't scale.

StorageClass automates storage creation.

```
PVC

↓

StorageClass

↓

Cloud Provider

↓

Disk Created Automatically
```

This is called **dynamic provisioning**.

Example:

```yaml
storageClassName: standard
```

---

# Dynamic Provisioning

Without StorageClass:

```
Admin Creates PV

↓

Developer Creates PVC

↓

PVC Binds to PV
```

With StorageClass:

```
Developer Creates PVC

↓

StorageClass

↓

Automatically Creates PV

↓

PVC Bound
```

---

# CSI (Container Storage Interface)

CSI is a standard interface that lets Kubernetes communicate with different storage providers.

Examples:

- AWS EBS CSI Driver
- Azure Disk CSI Driver
- Google PD CSI Driver
- Ceph CSI Driver
- NFS CSI Driver

```
Kubernetes

↓

CSI Driver

↓

Cloud Storage
```

---

# Access Modes

## ReadWriteOnce (RWO)

One node can mount the volume with read/write access.

Used by:

- AWS EBS
- Azure Disk

---

## ReadOnlyMany (ROX)

Multiple nodes can read the same volume.

---

## ReadWriteMany (RWX)

Multiple nodes can read and write simultaneously.

Used by:

- NFS
- CephFS
- Azure Files

---

# Volume Reclaim Policies

## Retain

```
PVC Deleted

↓

PV Remains
```

Data is preserved until manually cleaned up.

---

## Delete

```
PVC Deleted

↓

PV Deleted

↓

Underlying Storage Deleted
```

Common for cloud-managed disks.

---

## Recycle (Deprecated)

Previously cleared data and reused the volume.

No longer recommended.

---

# Complete Storage Flow

```
Deployment

↓

Pod

↓

PVC

↓

StorageClass

↓

PV

↓

Cloud Disk
```

---

# Useful Commands

## View Persistent Volumes

```bash
kubectl get pv
```

---

## View PVCs

```bash
kubectl get pvc
```

---

## Describe PV

```bash
kubectl describe pv <pv-name>
```

---

## Describe PVC

```bash
kubectl describe pvc <pvc-name>
```

---

## View StorageClasses

```bash
kubectl get storageclass
```

or

```bash
kubectl get sc
```

---

## Describe StorageClass

```bash
kubectl describe storageclass standard
```

---

## Delete PVC

```bash
kubectl delete pvc my-pvc
```

---

## Delete PV

```bash
kubectl delete pv my-pv
```

---

# Common Errors

## PVC Pending

Reason:

No matching PV or no StorageClass available.

Check:

```bash
kubectl describe pvc
```

---

## Mount Failed

Possible reasons:

- Wrong access mode
- Node can't attach storage
- CSI driver issue

Check:

```bash
kubectl describe pod
```

---

## Volume Not Found

Check:

```bash
kubectl get pv
kubectl get pvc
```

---

# Best Practices

✅ Use PVCs instead of hardcoding PV names.

✅ Prefer StorageClasses for dynamic provisioning.

✅ Use StatefulSets for stateful applications.

✅ Choose the correct access mode for your workload.

✅ Back up persistent data regularly.

---

# Production Tips

- Databases (MySQL, PostgreSQL, MongoDB) should use **Persistent Volumes**, not `emptyDir`.
- Avoid `hostPath` in production unless you fully understand the scheduling implications.
- Monitor storage usage to prevent applications from running out of disk space.
- Use the cloud provider's CSI driver instead of deprecated in-tree storage plugins.

---

# Interview Questions

## Beginner

1. What is a Volume?
2. Why do containers lose data?
3. Difference between Volume and Persistent Volume?
4. What is a PVC?
5. What is a StorageClass?

---

## Intermediate

6. Explain dynamic provisioning.
7. Explain the PV-PVC binding process.
8. What is CSI?
9. Difference between `emptyDir` and `hostPath`?
10. Difference between ephemeral and persistent storage?

---

## Advanced

11. Explain access modes (RWO, ROX, RWX).
12. Explain reclaim policies.
13. How would you migrate storage to a larger disk?
14. Why are StatefulSets commonly used with Persistent Volumes?

---

# 📝 Quick Revision

- Containers are ephemeral; persistent data requires external storage.
- Volumes provide storage to Pods.
- `emptyDir` is temporary and deleted with the Pod.
- `hostPath` mounts a node directory and is mainly for development or special use cases.
- PV = Storage resource managed by the cluster.
- PVC = Storage request made by an application.
- StorageClass enables automatic (dynamic) provisioning.
- CSI is the standard interface between Kubernetes and storage providers.
- Use StatefulSets with Persistent Volumes for stateful workloads.
