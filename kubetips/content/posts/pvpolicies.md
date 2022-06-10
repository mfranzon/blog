---
title: "PersistenVolume: choose the correct policy to avoid bad surprises. Don't lose your data!"
date: 2022-06-09T23:41:44+02:00
authors: ["Marco Franzon"]
thumbnail: "twitter-shared-link.png"
images: ["twitter-shared-link.png"]
---

PersistenVolume (PV) is a Kubernetes resource, that gives you the possibility to obtain storage from the infrastructure without worry about the storage technologies.

### Create a PV

Creating a PV follow the same rules as for the other Kubernetes resources.
Let's have a look on an example file:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: $name-pv
spec:
  capacity:
    storage: 1Gi
    accessModes:
    - ReadWriteOnce
    - ReadOnlyMany
    persistentVolumeReclaimPolicy: {Delete, Retain, Recycle}
```

Three main keywords pop up in this .yaml file:
- `storage`, simply the size of this volume
- `accessModes`, which suggests how many services can read/write on that volume. In this example it can be mounted for reading and write by a single client (pod), but could read from many.
- `persistentVolumeReclaimPolicy`, the name self-explained what is its behaviour. Choose between one of the available policies `{Delete, Retain, Recycle}`

Pay attention on which Reclaim policy you choose, or which is the default one.
You may end up in a bad surprise, losing your volume (and data) after deleting a pod.

### PV policies in detail
Understanding the main differences between pv policies, could be very useful to ensure the correct behaviour of your data.

- `Delete` deletes the volume and its content when the linked pod/s is/are deleted

- `Recycle` deletes the volume’s contents and makes the volume available to be claimed again. This way, the PersistentVolume can be reused multiple times by different PersistentVolumeClaims and different pods

- `Retain` does not delete the volume's content and remains in a Pending status when the linked pod is deleted. Then, a new pod using the same PersistentVolume could read the data stored there by the previous pod, even if the claim and pod were created in a different namespace.

### Access Modes

The available access modes have also an important role to avoid concurrency or data loss. If you give the wrong permissions, could happen that the volume can be overwritten from different clients/pods.

The possible choices are:
- `ReadWriteOnce` only a single node can mount the volume for reading and writing
- `ReadOnlyMany` multiple nodes can mount the volume for reading
- `ReadWriteMany` multiple nodes can mount the volume for both reading and writing
