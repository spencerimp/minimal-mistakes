---
layout: single
title: Kubernetes on AWS and Google Cloud
description: ""
category:
tags: []
permalink: /2017/06/23/kubernetes-on-aws-and-google-cloud/
---
In order to practice, I edit and supplement the post from

[http://christopher5106.github.io/continous/deployment/2016/05/02/deploy-instantly-from-your-host-to-AWS-EC2-and-Google-Cloud-with-kubernetes.html](http://christopher5106.github.io/continous/deployment/2016/05/02/deploy-instantly-from-your-host-to-AWS-EC2-and-Google-Cloud-with-kubernetes.html)

Kubernetes is an open-source framework for container cluster management based on Docker.

As a developer, one could use Kubernetes on their cloud clusters to achieve faster and reliable deployment.
Also, Kubernetes supports auto-scaling (load balancing), which is important for production.

What will be covered

- Use Docker to build and run images.
- Create a cluster on AWS or GCP.
- Deploy Docker images to AWS or GCP.
- Kubernetes configuration examples.

## Build a Docker image
Before the image can be deployed to the cloud cluster, and image has to be built first. You cannot deploy the image by a Dockerfile.

Here I use the ubuntu image as our base image, you can have your custom image.

Build an image (ubuntu) from DockerHub registry and tag it as my-img
```
docker run ubuntu -t my-img
```

# Deploy the image to a cloud cluster

- Set account on the cloud platform.
- Get credential for the container service on cloud.
- Create a cluster.
- Tag the image with the container registry format.
- Push the image to container registry AWS (ecr) or  GCP (gkc).


For ECR:
AWS_ACCOUNT_ID.dkr.ecr.REGION.amazonaws.com/image

For GCP
gcr.io/PROJECT_NAME/image

## Push image to registry (i.e. online Docker image hosting site)
```
# aws
# Attach ECR access in AWS console at IAM policy
#https://console.aws.amazon.com/iam/home?region=eu-central-1#/home
aws
# you can get the account id by
aws sts get-caller-identity --output text --query 'Account'
aws ecr create-repository --repository-name my-img

$(aws ecr get-login) # Yes, you need to type $()
docker tag my-img AWS_ACCOINT_ID.dkr.ecr.eu-central-1.amazonaws.com/my-img
docker push my-img AWS_ACCOINT_ID.dkr.ecr.eu-central-1.amazonaws.com/my-img

# gcp
docker tag my-img gcr.io/PROJECT_NAME/my-img
gcloud docker -- push gcr.io/PROJECT_NAME/my-img
```

## Install Kubernets
```
# aws
export KUBERNETES_PROVIDER=aws; wget -q -O - https://get.k8s.io | bash

# gcp
gcloud components install kubectl
# or export KUBERNETES_PROVIDER=gke
```

## Create cluster
```
#aws
# http://docs.aws.amazon.com/cli/latest/reference/ecs/
aws ecs --create-cluster my-cluster

# gcp
gcloud container clusters create-cluster my-cluster
gcloud container clusters get-credentials my-cluster
gcloud container clusters list
gcloud config set container/cluster my-cluster # set as default cluster
```

## Use Kubernetes to deploy pod/container on the cluster
```
kubectl run my-node --image=[image] --port=8080
kubectl get deployment my-node
#NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
#my-node    3         3         3            0           1s
```

## Alternatively, you can deploy a pod file
Create a file, and name as pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: my-node
spec:
  containers:
    - name: my-app
      image: gcr.io/tf-gpu-test/ubuntu
      ports:
        - containerPort: 8080
```
Then deploy the pod from a file (similar to run a Docker container from a file)
```
kubectl -f pod.yaml
```
## Export the deployment to the Internet (make a service)
```
kubectl expose deployment my-node
kubectl get service
kubectl get service my-node
```

## Scale the number of replicas
```
kubectl scale deployment my-node --replicas=5
kubectl get deployment my-pod
#NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
#my-node    5         5         5            0           20m
```
## Auto scaling
```
# number of pods between 2 to 10, target average CPU utilization at 80%
kubectl autoscale deployment [DEPLOYMENT] --min=2 --max=10 --cpu-percent=80
```

## Delete
```
kubectl delete service my-node
kubectl delete deployment my-node

# aws
aws ecr delete-repository --repository-name my-cluster
aws ecr describe-repositories
aws ecs delete-cluster my-cluster
aws ecs list-clusters

# gcp
gcloud container images delete [image]
gcloud container images list
gcloud container clusters delete my-cluser
gcloud container clusters list
```

# Run Kubernetes locally
Normally, Kubernetes starts to work after you set up the cluster. However, you can test Kubernete locally (e.g. on you laptop).

You can install minikube and replace the kutectl commands by minikube.

## From Docker Compose to Kubernetes

[https://github.com/kubernetes-incubator/kompose](https://github.com/kubernetes-incubator/kompose)

You may know Docker Compose, which allows you to configure multi-container services in single docker-compose yaml. However, its support for cluster monitoring and management is limited.

You could switch to Kubernetes, which separates the configuration into Pods, Services, etc. Fortunately, you can use install kompose to do all the conversion for you.

For example
```
# convert and run the docker-compose.yaml to Kubernetes unit.
kompose up

# Alternative, you can get the files into a folder
kompose convert
```

## Spark + Kubernetes
Consider checking the example provided by Kubernetes to know how to set up your Spark cluster by Kubernetes configuration.

```
git clone https://github.com/kubernetes/kubernetes
cd kubernetes/examples/spark
# create namespace
kubectl create -f namespace-spark-cluster.yaml

# Launch Spark master service
kubectl create -f spark-master-controller.yaml # replication controller for pod
kubectl create -f spark-master-service.yaml # expose as a service

# Launch Spark slave (only communicate with master)
kubectl create -f spark-worker-controller.yaml

# You may use zeppelin to submit Spark jobs
kubectl create -f examples/spark/zeppelin-controller.yaml

# Check whether Spark is working
kubectl get pods -l component=zeppelin
kubectl exec [zeppelin-xxxxx] -it pyspark
```

# Other references
[https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)
[https://www.packtpub.com/books/content/setting-kubernetes-cluster](https://www.packtpub.com/books/content/setting-kubernetes-cluster)
[https://goo.gl/E17T5j](https://goo.gl/E17T5j)
