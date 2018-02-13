## Create a GKE clusters

Authenticate
```
gcloud auth login
```

```
export PROJECT_ID="autosuggest-194816"
export IMAGE_NAME="viralmar-site"
export IMAGE_VERSION="v1.1.6"
export CONTAINER_NAME="viralmar-site"
export CLUSTER_NAME="autosuggest-cluster"
export COMPUTE_ZONE="europe-west1-d"
export MACHINE_TYPE="n1-standard-1"
export NUM_NODES="2"
export DISK_SIZE="40"
```

Let's create a cluster for our site

Notes: 
- Machine types => https://cloud.google.com/compute/docs/machine-types
- Compute zones => https://cloud.google.com/compute/docs/regions-zones/regions-zones

** In order to deploy to App Engine, delete Dockerfile y => gcloud app deploy --project=viralmar-site

```
gcloud container clusters create $CLUSTER_NAME \
  --num-nodes=$NUM_NODES \
  --machine-type=$MACHINE_TYPE \
  --disk-size=$DISK_SIZE
```

## Get credentials

gcloud container clusters get-credentials autosuggest-cluster --zone europe-west1-d --project autosuggest-194816

## Get URL
gcloud container clusters describe autosuggest-cluster --zone europe-west1-d 

## Get nodes

kubectl get nodes
NAME                                                 STATUS    ROLES     AGE       VERSION
gke-autosuggest-cluster-default-pool-dae34eb8-2qn5   Ready     <none>    1d        v1.7.12-gke.1
gke-autosuggest-cluster-default-pool-dae34eb8-hp17   Ready     <none>    1d        v1.7.12-gke.1


## Create a namespace

kubectl create namespace autosuggest-dev

## Create frontend app
kubectl create -n autosuggest-dev -f ./autosuggest-app.yaml
kubectl expose deployment autosuggest-app-deployment --type=LoadBalancer --name=autosuggest-app-service



## Create your Kubernetes Cluster

A cluster consists of a Master API server and a set of worker VMs called Nodes.
First, choose a Google Cloud Project zone to run your service. For this tutorial, we will be using us-central1-a. This is configured on the command line via:

```
gcloud config set compute/zone $COMPUTE_ZONE
```

Now, create a cluster via the gcloud command line tool:

```
gcloud container clusters create $CLUSTER_NAME \
>   --num-nodes=1 \
>   --machine-type=g1-small \
>   --disk-size=10
Creating cluster viralmar-site...done.                                                                                                       
Created [https://container.googleapis.com/v1/projects/pivotal-mile-160908/zones/europe-west1-d/clusters/viralmar-site].
kubeconfig entry generated for viralmar-site.
NAME           ZONE            MASTER_VERSION  MASTER_IP      MACHINE_TYPE  NODE_VERSION  NUM_NODES  STATUS
viralmar-site  europe-west1-d  1.5.3           104.155.40.79  g1-small      1.5.3         1          RUNNING
```

It’s now time to deploy your own containerized application to the Kubernetes cluster!

```
gcloud container clusters get-credentials $CLUSTER_NAME
```

## Create your pod

A Kubernetes pod is a group of containers, tied together for the purposes of administration and networking. It can contain a single container or multiple.
Create a Pod with the kubectl run command:

```
kubectl run $CONTAINER_NAME --image=gcr.io/$PROJECT_ID/$IMAGE_NAME:v1 --port=8080
```

As shown in the output, the kubectl run created a Deployment object. Deployments are the recommended way for managing creation and scaling of pods. In this example, a new deployment manages a single pod replica running the hello-node:v1 image.
To view the Deployment we just created run:

```
kubectl get deployments
NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
viralmar-site   1         1         1            0           18s
```

To view the Pod created by the deployment run:
```
kubectl get pods

NAME                             READY     STATUS              RESTARTS   AGE
viralmar-site-3644510091-881rf   0/1       ContainerCreating   0          1m
```

To view the stdout / stderr from a Pod run (probably empty currently):

```
kubectl logs <POD-NAME>
```

To view metadata about the cluster run:

```
kubectl cluster-info
```

To view cluster events run:

```
kubectl get events
```

To view the kubectl configuration run:

```
kubectl config view
```

### Allow external traffic

By default, the pod is only accessible by its internal IP within the Kubernetes cluster. In order to make the hello-node container accessible from outside the Kubernetes virtual network, you have to expose the Pod as a Kubernetes Service.
From our Development machine we can expose the pod to the public internet using the kubectl expose command combined with the --type="LoadBalancer" flag. The flag is needed for the creation of an externally accessible ip:

```
kubectl expose deployment $CONTAINER_NAME --type="LoadBalancer"
```

To find the ip addresses associated with the service run:
```
kubectl get services $CONTAINER_NAME

NAME            CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
viralmar-site   10.107.241.37   <pending>     8080:32110/TCP   9s
```

The EXTERNAL_IP may take several minutes to become available and visible. If the EXTERNAL_IP is missing, wait a few minutes and try again.

```
kubectl get services $CONTAINER_NAME

NAME            CLUSTER-IP      EXTERNAL-IP     PORT(S)          AGE
viralmar-site   10.107.241.37   146.148.29.51   8080:32110/TCP   1m
```