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

## Create a Docker container image

Next, create a file, also within named Dockerfile. A Dockerfile describes the image that you want to build. Docker container images can extend from other existing images so for this image, we’ll extend from an existing Node image.

### Dockerfile

```
FROM node:4.4
EXPOSE 8080
COPY server.js .
CMD node server.js
```

This “recipe” for the Docker image will start from the official Node.js LTS image found on the Docker registry, expose port 8080, copy our ``server.js`` file to the image and start the Node server.
Now build an image of your container by running docker build, tagging the image with the Google Container Registry repo for your $PROJECT_ID:

```
docker build -t gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION .
```

Now there is a trusted source for getting an image of your containerized app.
Let’s try your image out with Docker:

```
docker run -d -p 8080:8080 --name $CONTAINER_NAME gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION
```

Visit your app in the browser, or use curl or wget if you’d like :

```
curl http://localhost:8080
```

You should see Hello World!
Note: If you receive a Connection refused message from Docker for Mac, ensure you are using the latest version of Docker (1.12 or later). Alternatively, if you are using Docker Toolbox on OSX, make sure you are using the VM’s IP and not localhost:
curl "http://$(docker-machine ip YOUR-VM-MACHINE-NAME):8080"
Let’s now stop the container. You can list the docker containers with:

```
docker ps
```

You should see something like this:

```
CONTAINER ID        IMAGE                                 COMMAND                  NAMES
c5b6d4b9f36d        gcr.io/$PROJECT_ID/hello-node:v1      "/bin/sh -c 'node ser"   hello_tutorial
```

Now stop the running container with

```
docker stop $CONTAINER_NAME
```

Now that the image works as intended and is all tagged with your $PROJECT_ID, we can push it to the Google Container Registry, a private repository for your Docker images accessible from every Google Cloud project (but also from outside Google Cloud Platform) :

```
gcloud docker push gcr.io/$PROJECT_ID/$IMAGE_NAME:$IMAGE_VERSION
```

```
WARNING: The '--' argument must be specified between gcloud specific args on the left and DOCKER_ARGS on the right. IMPORTANT: previously, commands allowed the omission of the --, and unparsed arguments were treated as implementation args. This usage is being deprecated and will be removed in March 2017.
This will be strictly enforced in March 2017. Use 'gcloud beta docker' to see new behavior.
Using 'push gcr.io/pivotal-mile-160908/viralmar-site:v1' for DOCKER_ARGS.
The push refers to a repository [gcr.io/pivotal-mile-160908/viralmar-site]
13ec01fef4ee: Pushed
20a6f9d228c0: Pushed
80c332ac5101: Pushed
04dc8c446a38: Pushed
1050aff7cfff: Pushed
66d8e5ee400c: Pushed
2f71b45e4e25: Pushed
v1: digest: sha256:aa29c722f3f9f26bd58220e66b5310c74421ee9cb9e0427ecedaaf3a14019877 size: 1794
```

If all goes well, you should be able to see the container image listed in the console: Compute > Container Engine > Container Registry. We now have a project-wide Docker image available which Kubernetes can access and orchestrate.

If you see an error message like the following: denied: Unable to create the repository, please check that you have access to do so. ensure that you are pushing the image to Container Registry with the correct user credentials, use gcloud auth list and then gcloud config set account example@gmail.com.



Note: Docker for Windows, Version 1.12 or 1.12.1, does not yet support this procedure. Instead, it replies with the message ‘denied: Unable to access the repository; please check that you have permission to access it’. A bugfix is available at http://stackoverflow.com/questions/39277986/unable-to-push-to-google-container-registry-unable-to-access-the-repository?answertab=votes#tab-top

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