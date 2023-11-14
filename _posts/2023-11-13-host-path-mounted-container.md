---
title: "K8S: HostPath Mounted Container"
author: rjulian
date: 2023-11-09 09:19:00 -0500
categories: [Kubernetes]
tags: [k8s, reconnaissance, permissions]
---

## Overview

HostPath volumes are, as named, a way to mount a path directly from the host to a running pod on the system. 

Clearly, this can be a massive problem with a similar exploitation to [privileged containers](../privileged-container/), but it also opens an organization up to a great reconnaissance opportunity.

## Deployment

### Code
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dvldb-host-path-mount-container-service-c8875b5b
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: host-path-mount-container
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dvldb-host-path-mount-container-deployment-c8ad4fcb
spec:
  replicas: 2
  selector:
    matchLabels:
      app: host-path-mount-container
  template:
    metadata:
      labels:
        app: host-path-mount-container
    spec:
      containers:
        - image: nginx:latest
          name: nginx
          ports:
            - containerPort: 8080
          volumeMounts:
            - mountPath: /mounted/
              name: host-mounted
      volumes:
        - hostPath:
            path: /var/log/
          name: host-mounted
```

### Implementation

```bash
kubectl apply -f dvldb-host-path-mount-container.k8s.yaml
```

## Exploitation

### Reconnaissance

As mentioned, you could absolutely wreak havoc by mounting a sensitive host directory to the container. 

For the purposes of this DVL though, it's more interesting to look at the reconnaissance opportunity. In our example, we can see that `/var/log` has been mounted to the running pod, giving that pod (and whoever is able to get into it) unfettered access to read the logs of all services on our system. This is a wonderful way of gathering information for a potential lateral movement.

## Resources

* https://kubernetes.io/docs/concepts/security/pod-security-standards/
* https://blog.appsecco.com/kubernetes-namespace-breakout-using-insecure-host-path-volume-part-1-b382f2a6e216
