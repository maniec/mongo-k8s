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
```