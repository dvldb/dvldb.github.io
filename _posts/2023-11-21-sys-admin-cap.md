---
title: "K8S: Sys Admin Capability"
author: rjulian
date: 2023-11-21 11:33:00 -0300
categories: [Kubernetes]
tags: [k8s, permissions]
---

## Overview

Limiting usage of certain Linux capabilities is essential to preventing privilege escalation and further exploitation of your application infrastructure.

Note that you can mitigate this risk by specifying unneeded capabilities using the `spec.containers[*].securityContext.capabilities.drop` convention.


## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-sys-admin-cap-container-service-c883f404
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: sys-admin-cap-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-sys-admin-cap-container-deployment-c8dd70d3
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sys-admin-cap-container
  template:
    metadata:
      labels:
        app: sys-admin-cap-container
    spec:
      containers:
        - image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
          securityContext:
            capabilities:
              add:
                - SYS_ADMIN
```

### Implementation

```bash
kubectl apply -f dvldb-sys-admin-cap-container.k8s.yaml
```

## Exploitation


## Resources

* https://kubernetes.io/docs/concepts/security/pod-security-standards/
* https://learn.microsoft.com/en-us/azure/defender-for-cloud/kubernetes-workload-protections#view-and-configure-the-bundle-of-recommendations 
