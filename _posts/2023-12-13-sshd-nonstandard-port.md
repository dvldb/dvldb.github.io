---
title: "K8S: sshd on Nonstandard Port"
author: rjulian
date: 2023-12-13 11:33:00 -0300
categories: [Kubernetes]
tags: [k8s, networking]
---

## Overview
It would be very unlikely to see sshd installed and serving over port 22 within your kubernetes cluster. But, could you identify if it was serving traffic over port 8080 or other ports associated with fairly normal services in k8s. 

This DVL is VERY insecure in that it not only serves SSH traffic over port 8080, but also allows unpasswordless login to root. 


## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nonstandard-sshd-service
spec:
  ports:
    - port: 8080
      targetPort: 8080
  selector:
    app: dvldb-sshd-nonstandard-port-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-sshd-nonstandard-port-container-deployment-c87e3a0e
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dvldb-sshd-nonstandard-port-container
  template:
    metadata:
      labels:
        app: dvldb-sshd-nonstandard-port-container
    spec:
      containers:
        - image: rjulian/sshdalpine:latest
          name: sshdalpine
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Pod
metadata:
  name: dvldb-sshd-nonstandard-port-container-sshclient-c846d610
spec:
  containers:
    - command:
        - sh
        - -c
        - ssh -p 8080 -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@nonstandard-sshd-service.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local "sleep 500"
      image: rjulian/sshdalpine:latest
      name: sshdalpine-client
  initContainers:
    - command:
        - sh
        - -c
        - until nslookup nonstandard-sshd-service.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done
      image: busybox:latest
      name: init-sshd-service
```

### Implementation

```bash
kubectl apply -f dvldb-sshd-nonstandard-port-container.k8s.yaml 
```

## Exploitation

Externally, it is possible to ssh into the instance to further exploit, but the included single pod will also SSH into the vulnerable service. 


## Resources

