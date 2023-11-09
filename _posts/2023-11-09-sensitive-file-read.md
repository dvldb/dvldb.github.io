---
title: "K8S: Sensitive Files Read"
author: rjulian
date: 2023-11-09 09:19:00 -0500
categories: [Kubernetes]
tags: [k8s, anomaly detection, reconnaissance]
---

## Overview

When running an application inside a container environment, it's not very common for the application to read files specific to the underlying operating system. In our case, we're looking at reading any file in the `/etc/` subdirectory.

Generally, this access, especially after the initial run of a container, is considered to be a signal of reconnaissance. 

There're absolutely cases where this could be required by your application and for some organizations, this would be an exception.

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-sensitive-read-container-service-c8838208
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: dvldb-sensitive-read-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-sensitive-read-container-deployment-c879cc34
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dvldb-sensitive-read-container
  template:
    metadata:
      labels:
        app: dvldb-sensitive-read-container
    spec:
      containers:
        - args:
            - /etc/shadow
          command:
            - cat
          image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
```

### Implementation

```bash
kubectl apply -f dvldb-sensitive-read-container.k8s.yaml
```

## Exploitation

No exploitation as part of this, yet feel free to go into the container and open many other files in the /etc/ directory that wouldn't normally be open to be read by an application. This will further serve evidence of reconnaissance. 

## Resources

* https://falcosecurity.github.io/rules/#stable-falco-rules - Check out "Read sensitive file trusted after startup"
