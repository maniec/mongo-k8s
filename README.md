# Mongo + Mongo Express on K8s

## Init Project
```shell
mkdir mongo-k8s
cd mongo-k8s
git init
git branch -m main
touch .gitignore
touch README.md
git add .
git commit -m "initial commit"
# create repo in GitHub 
git remote add origin https://github.com/maniec/mongo-k8s.git
git push -u origin main
idea .
```
## Overview
```
Wec Client
    Deployment
        Service (External)
            Mongo Express (Pod)
                ConfigMap
                    Service (Internal)
                        Secret
                            Mongo DB (Pod) 
```
## K8s Resources
- 2 x Deployment
- 2 x Service
- 1 x ConfigMap
- 1 x Secret

## `minikube` 
```shell
minikube start --driver=docker
minikube status
kubectl get all
``` 

## Secrets encoding
```shell
# DO NOT store secret file into repo
echo -n 'yoursecrethere' | base64
```

## **_Resource_** creation order
The creation order of _**Resources**_ matters because they have dependencies like env variables and secrets

Those dependencies have to be available at the time its dependent **_resources_** pull them

```shell
kubectl apply -f mongo-secret.yaml
kubectl get secret

kubectl apply -f mongo.yaml
kubectl get all
kubectl get pod
kubectl get pod --watch
kubectl describe pod mongodb-deployment-844789cd64-2d4r9

kubectl get service
# check types, ports, etc
# NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
# service/mongodb-service   ClusterIP   10.106.172.19   <none>        27017/TCP   17m
kubectl describe service mongodb-service
# check whether attached to the correct Pod by looking at Endpoints and comparing IPs
# Name:              mongodb-service
# Selector:          app=mongodb
# Type:              ClusterIP
# TargetPort:        27017/TCP
# Endpoints:         172.17.0.3:27017
kubectl get pod -o wide
# NAME                                  READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
# mongodb-deployment-844789cd64-vsbwn   1/1     Running   0          23m   172.17.0.3   minikube   <none>           <none>
kubectl get all | grep mongo

kubectl apply -f mongo-configmap.yaml
kubectl apply -f mongo-express.yaml
kubectl get all | grep mongo
kubectl logs mongo-express-5bf4b56f47-dd9xz
```

## Services
- Internal
  - ClusterIP (default)
  - Has an internal IP address
  - Has an internal PORT assigned
- External
  - LoadBalancer
  - Has both, internal and external IP addresses
    - However as we are using minikube, external IP is handled a little different than a regular Kubernetes setup
  - Has also both, internal and external (web) PORT addresses
```shell
kubectl get service
# NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
# mongo-express-service   LoadBalancer   10.105.133.90   <pending>     8081:30000/TCP   15m
# mongodb-service         ClusterIP      10.106.172.19   <none>        27017/TCP        51m
minikube service mongo-express-service
```