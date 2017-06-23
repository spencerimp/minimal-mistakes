---
layout: single
title: Google Cloud SDK
description: ""
category:
tags: [google cloud, kubernetes]
permalink: /2017/06/23/google-cloud-sdk/
---
# Install Cloud SDK

[https://cloud.google.com/sdk/downloads"](https://cloud.google.com/sdk/downloads)

Unzip the source file

Note that the folder is your installation folder, select your preferred location.

```
sh ./install.sh
```
And allow the script to add the auto-completion.

# Log in
```
gcloud auth login
```

# Set application default credentials

```
gcloud auth application-default

#Credentials saved to file:
#[/Users/spencer/.config/gcloud/application_default_credentials.json]
#These credentials will be used by any library that requests
```

# Create a configuration

```
gcloud init
```

To show the list of configurations

```
gcloud config configurations list
```

To activate a different config

```
gcloud config configurations activate [config name]
```
------

# *Google Compute*

Create compute instance (google virtual machine)

[https://cloud.google.com/sdk/gcloud/reference/](https://cloud.google.com/sdk/gcloud/reference/)

# Create an instance from standard machine types
```
gcloud compute instances create --machine-type n1-standard-2 my-instance
```
You can check available machine types

```
gcloud compute machine-types

#NAME ZONE MACHINE_TYPE PREEMPTIBLE INTERNAL_IP #EXTERNAL_IP STATUS
#my-instance europe-west1-b n1-standard-2 10.132.0.2 104.155.70.152 RUNNING
```

# Detele an instance
```
gcloud compute instances delete my-instance
```

# Create a customized instance
```
gcloud compute instances create --custom-cpu 4 --custom-memory 4GB my-instance

# NAME ZONE MACHINE_TYPE PREEMPTIBLE INTERNAL_IP EXTERNAL_IP STATUS
#my-instance europe-west1-b custom (4 vCPU, 4.00 GiB) 10.132.0.2 130.211.88.0 RUNNING
```

# Connect to an instance
```
gcloud compute ssh my-instance
```

# Copy files from local to instance
```
gcloud compute scp myfile.txt my-instance:~/remote-dir
# conversely, you can copy from instance to local
```
------

# *Google Container*

# Create cluster for container
```
gcloud container clusters create my-cluster --machine-type n1-standard-2 --num-nodes 4
```
# Build a Docker image locally
```
docker build -f Dockerfile .
# or build from a registry
# docker run -t ubuntu
```

# Upload a Docker image to Container Registry
[https://cloud.google.com/container-registry/docs/pushing-and-pulling](https://cloud.google.com/container-registry/docs/pushing-and-pulling)

 
The registry should be formatted as (like you do with Dockerhub)

[HOSTNAME]/[YOUR-PROJECT-ID]/[IMAGE]

HOSTNAME can be one of

- us.gcr.io
- eu.gcr.io
- asia.gcr.io
- gcr.io

For example:

Image name: ubuntu

Register: gcr.io/my-project/ubuntu

```
docker tag ubuntu specerimp/my-project/ubuntu
gcloud docker -- push spencer/my-project/ubuntu
gcloud container images list
```
------
# *Kubernetes*

Use Kubernetes to create pod/container/ from registry on Google Cloud (or other platforms)

[https://cloud.google.com/container-engine/docs/quickstart](https://cloud.google.com/container-engine/docs/quickstart)

You need to get auth credentials and create a cluster before creating a pod

```
gcloud concainer clusters get-credentials my-cluster
gcloud container clusters create my-cluster
kubectl run my-pod --image=gcr.io/my-project/ubuntu
#deployment "my-pod" created
```

# Check pods on the cluster
```
kubectl get pods
#NAME READY STATUS RESTARTS AGE
#my-pod-2776559432-kfhj3 0/1 Completed 3 58s
```

# Deploy the container to cluster
In Kubernetes, it is called expose a service)

You HAVE to specify *--port* either in creating a pod or deployment

```
kubectl expose deployment my-pod
```

# Delete
```
kubectl delete services my-pod
gcloud clusters delete my-cluster
```

# References
[https://cloud.google.com/container-engine/docs/quickstart](https://cloud.google.com/container-engine/docs/quickstart)
