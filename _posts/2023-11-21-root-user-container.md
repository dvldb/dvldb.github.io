---
title: "K8S: Root User Container"
author: rjulian
date: 2023-11-21 11:33:00 -0300
categories: [Kubernetes]
tags: [k8s, permissions]
---

## Overview

Running a container as root is generally considered insecure for most applications. 

Note, kubernetes does has the ability to require non-root users by leveraging the `spec.securityContext.runAsNonRoot` flag set to `true`.

The CIS Benchmark for Docker includes the recommendation `4.2 Create a user for the container`.

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-root-user-container-service-c8c0c1ac
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: root-user-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-root-user-container-deployment-c8ebae25
spec:
  replicas: 2
  selector:
    matchLabels:
      app: root-user-container
  template:
    metadata:
      labels:
        app: root-user-container
    spec:
      containers:
        - image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
          securityContext:
            runAsUser: 0
```

### Implementation

```bash
kubectl apply -f dvldb-root-user-container.k8s.yaml
```

## Exploitation


## Resources

* https://kubernetes.io/docs/concepts/security/pod-security-standards/
* https://www.howtogeek.com/devops/why-processes-in-docker-containers-shouldnt-run-as-root/
