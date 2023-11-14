---
title: "K8S: Log4Shell Container"
author: rjulian
date: 2023-11-09 09:19:00 -0500
categories: [Kubernetes]
tags: [k8s, container images, supply chain]
---

## Overview

It was an early December everyone will remember in enterprise. [Log4Shell](https://en.wikipedia.org/wiki/Log4Shell) burst into our lives, causing security teams to scramble to identify and patch vulnerable applications. 

Now, Log4Shell lives on as the strongest reminder of a supply chain vulnerability so commonplace that virtually everyone working in the field at the time can identify it. But can your security tooling?

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-log4shell-container-service-c8e238a8
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: dvldb-log4shell-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-log4shell-container-deployment-c89efefa
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dvldb-log4shell-container
  template:
    metadata:
      labels:
        app: dvldb-log4shell-container
    spec:
      containers:
        - image: ghcr.io/christophetd/log4shell-vulnerable-app
          name: tomcat
          ports:
            - containerPort: 8080
```

### Implementation

```bash
kubectl apply -f dvldb-log4shell-container.k8s.yaml
```

## Exploitation


## Resources

* https://en.wikipedia.org/wiki/Log4Shell 
* https://news.sophos.com/en-us/2021/12/17/inside-the-code-how-the-log4shell-exploit-works/ 
* https://www.youtube.com/watch?v=krfW0rAm9BE
