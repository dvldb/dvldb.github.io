---
title: "K8S: Connection to K8S API Server"
author: rjulian
date: 2023-12-13 12:33:00 -0300
categories: [Kubernetes]
tags: [k8s, reconnaissance]
---

## Overview
This container is fairly simple: it deploys a vanilla nginx pod and then makes a network connection to the local kubernetes API.  

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-api-server-connection-container-service-c8586aba
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: dvldb-api-server-connection-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-api-server-connection-container-deployment-c8aadad4
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dvldb-api-server-connection-container
  template:
    metadata:
      labels:
        app: dvldb-api-server-connection-container
    spec:
      containers:
        - args:
            - http://kubernetes.default.svc.cluster.local/
          command:
            - curl
          image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
```

### Implementation

```bash
kubectl apply -f dvldb-api-server-connection-container.k8s.yaml 
```

## Exploitation

Not applicable, as this is simply to test reconnaissance detection.

## Resources

