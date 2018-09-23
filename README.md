# Kubernetes demo at the 28th GeneXus International Meeting

This demo was part of the session [Microservices, containers, and Kubernetes](https://meetings.genexus.com/session/microservices-docker-containers-and-kubernetes/ENG) held on Sep 25th, 2018 in Montevideo, Uruguay, as part of the 28th GeneXus International Meeting. Read more about the conference [here](http://www.genexus.com/gx28).

The presentation file is [here](res/MicroservicesContainersKubernetes.pdf), *Video recording of this session will be available soon.*

The purpose of the demo is showing some of the concepts of Kubernetes and Docker.

For timing purposes, I had already created the images needed for this demo, but you can check out the source Knowledge Base from **[INSERT GXServer HERE]** and create one yourself.

The dockerfiles for the created images looks like the following:

```docker
# Dockerfile generated by GeneXus (.NET Core)
FROM microsoft/dotnet:2.1.1-aspnetcore-runtime
LABEL maintainer="seba gomez <sgomez@genexus.com>"
ENV GXAPPVERSION=v1
WORKDIR /app
COPY ["GX28/20180923122144", "/app"]
ENTRYPOINT ["dotnet", "bin/GxNetCoreStartup.dll"]
```

## Requirements

You need to have [Docker for Windows]() or ([Docker for Mac]()) installed on your computer. The newer versions of this client have already support for Kubernetes, so once you install the client, make sure you enable Kubernetes by selecting 'Enable Kubernetes' in the Docker Settings pane. 

![](res/EnableKubernetes.png?raw=true)

Make sure you have the green icon at the bottom left corner of your settings panel stating that 'Kubernetes is running'.

Also, if you want the clone this repo you will need git installed on your machine.

Now we're ready to go.

## Get this repository

Actually, what's important for this repository is the [gx28k8sdemo.yaml](gx28k8sdemo.yaml) file, so you can simply download it. But in case you want it all, create a folder and from a command line and run the following command.

```
git clone https://github.com/sebagomez/gx28k8sdemo.git .
```

## Start up the cluster

We are going to apply the following 'desired state' to the cluster. It basically spins up 5 instances of an application based in an image [sebagomez/gx28k8sdemo:v1](https://hub.docker.com/r/sebagomez/gx28k8sdemo/tags/) currently hosted in the Docker public registry at https://hub.docker.com/r/sebagomez/gx28k8sdemo. 

So to start the application run the following command

```
kubectl apply -f gx28k8sdemo.yaml
```
(that's assuming you're located in the same foldr the yaml file is)

This is the output you should get:
```
> deployment.extensions "gx28k8sdemo" created  
> service "gx28k8sdemo-service" created
```

Now we need to check what port was assigned to our application by Kubernetes. In order to do that we'll to get info about the created service.

```
kubectl get service gx28k8sdemo-service
```
This is the output I got, so in my case, the assigned port is 30361, it'll most certainly be different in your case.

```
NAME                  TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
gx28k8sdemo-service   NodePort   10.107.133.248   <none>        80:30361/TCP   2m
```

So, now everything I have to do is open my browser and go to http://localhost:30361 <-- keep in mind 30361 is my port.

![](res/appv1.png?raw=true)

## Upgrade the app

To upgrade the app to v2, all we have to do is modify our gx28k8sdemo.yaml file by replacing v1 by v2 in the image to use.

After that is done, apply again the same file to the cluster.

```
kubectl apply -f gx28k8sdemo.yaml
```

Now go back to your browser an hit refresh... it might take a while, but after a few seconds you should be able to see the version 2 of the app.

![](res/appv2.png?raw=true)

### See it in the dashboard

To start the dashboard you need to run the following command:

```
kubectl proxy
```

That will fire up the dashboard in your machine in port 8001, so you can now go to http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/overview?namespace=default

![](res/dashboard.png?raw=true)

There you will see the deployment, service and running pods. Go ahead and delete some pods, see what happens.

And that's it... let me know how this demo went.