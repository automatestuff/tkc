# Readme

## Overview
Deploy a VMware Harbor Container to a Tanzu Kubernetes Cluster (TKC) and configure basic elements

## Prerequisites
- vSphere 7.0 Update 2 (or newer)
- Workload Management Namespace
- Tanzu Kubernetes Cluster w/ sufficient storage
    - [Sample sharedservices TKC YAML](./resources/shared-services-cluster.yaml)

## Steps

 0. Ensure that your kubectl session is set to the right context
    - `kubectl config current context`
 1. Configure cluster to use RBAC Authentication
    - `kubectl apply -f tanzu-cr.yaml`
 2. (Optionally) load CA signed certificate
    - `kubectl apply -f ca-issued-cert.yaml`
 3. Add stable to Helm repo 
    - `helm repo add stable https://charts.helm.sh/stable`
 4. Add bitnomi to Helm repo
    - `helm repo add bitnami https://charts.bitnami.com/bitnami`
 5. Install NGINX
    - `helm install nginx-ingress stable/nginx-ingress --set controller.config.proxy-body-size=1024m`
 6. Install Harbor
    - `helm install harbor bitnami/harbor --version 10.2.2 -f harbor-values.yaml`
        - [Sample harbor-values YAML](./resources/harbor-values.yaml)
 5. Follow instructions on the screen to decode the password and logon to Harbor