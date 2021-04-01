

<h1 align="center">k8-go-infra</h1>

<p align="center">
    <a href="https://github.com/k8-proxy/go-k8s-infra/actions/workflows/build.yml">
        <img src="https://github.com/k8-proxy/go-k8s-infra/actions/workflows/build.yml/badge.svg"/>
    </a>
	<a href="https://github.com/k8-proxy/go-k8s-infra/pulls">
        <img src="https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat" alt="Contributions welcome">
    </a>
    <a href="https://opensource.org/licenses/Apache-2.0">
        <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="Apache License, Version 2.0">
    </a>
</p>


# go-k8-infra
Go based Kubernetes infrastructure, providing an ICAP based solution to process files through Glasswall CDR engine.

The work here is a continuation and enhancement over: https://github.com/k8-proxy/icap-infrastructure).

## Version v0.x:

### Solution diagram
![Solution Diagram](https://github.com/k8-proxy/go-k8s-infra/raw/main/diagram/go-k8s-infra.png)
### Enhancements
The solution provides the following enhancements:
- Using MinIO instead of local storage for the processing pods.
- Implementing process pods hot reload feature.


### Components
The solution consists of the following components:
- [Service 1](https://github.com/k8-proxy/go-k8s-srv1)
- [Service 2](https://github.com/k8-proxy/go-k8s-srv2)
- [Processing Service.](https://github.com/k8-proxy/go-k8s-process)
- [Controller Service.](https://github.com/k8-proxy/go-k8s-controller)
- Components related to ICAP server, RabbitMQ, transaction logs , Management UI and similar (From here: https://github.com/k8-proxy/icap-infrastructure).


## Setup from scratch

- Install k8s

- Install helm

- Install kubectl

- Deploy icap-server

- Scale the existing adaptation service to 0
```
kubectl -n icap-adaptation scale --replicas=0 deployment/adaptation-service
```

- Export minio tls cert 
```
kubectl -n minio get secret/minio-tls -o "jsonpath={.data['public\.crt']}" | base64 --decode > /tmp/minio-cert.pem
```

- Import to adaptation service as a configmap 
```
kubectl -n icap-adaptation create configmap minio-cert --from-file=/tmp/minio-cert.pem
```

- Create minio credentials secret
```
kubectl create -n icap-adaptation secret generic minio-credentials --from-literal=username='<minio-user>' --from-literal=password='<minio-password>'
```

- Apply helm chart to create the services
```
helm upgrade servicesv2 --install . --namespace icap-adaptation
```

## Setup

- Deploy a vm from an icap-server AMI with minio installed. Example AMI : ami-099a48891215f2699

- Scale the existing adaptation service to 0
```
kubectl -n icap-adaptation scale --replicas=0 deployment/adaptation-service
```

- Export minio tls cert 
```
kubectl -n minio get secret/minio-tls -o "jsonpath={.data['public\.crt']}" | base64 --decode > /tmp/minio-cert.pem
```

- Import to adaptation service as a configmap 
```
kubectl -n icap-adaptation create configmap minio-cert --from-file=/tmp/minio-cert.pem
```

- Create minio credentials secret
```
kubectl create -n icap-adaptation secret generic minio-credentials --from-literal=username='<minio-user>' --from-literal=password='<minio-password>'
```

- Apply helm chart to create the services
```
helm upgrade servicesv2 --install . --namespace icap-adaptation
```

## Test

For testing, try to rebuild a file


