---
title: "Why Minikube is a great alternative to Docker Desktop"
subtitle: "The road to Kubernetes"
layout: post
author: "Malte"
header-style: text
tags:

- Minikube
- Docker
- Docker-Desktop
- Kubernetes

---

## Introduction: What is Minikube?

Minikube is a single node Kubernetes cluster that runs in a virtual machine on your laptop. It is great for development
and testing because it is easy to get started and you don't need to install any external software. You can use Minikube
to deploy applications, experiment with Kubernetes features, and learn how to use Kubernetes, which is an excellent
option for having a simple Kubernetes cluster up and running on localhost.

Advantages of Minikube:
Minikube is a great alternative to docker desktop. It is an open source project, driven by the Kubernetes community.
Especially for commercial Mac users, it is getting more important. Since Docker has changed the pricing model for Docker
Desktop. Latest News you can checkout on https://www.docker.com/pricing/.

Furthermore you can install Minikube on Mac, Windows, and Linux.

In this guide, we will install Minikube over brew on macOS and focus on the most important commands to replace Docker
Desktop.

1. Installation commands

First of all install Minikube via brew:

```
brew install minikube
```

It sounds counterintuitive, but install docker as well:

```
brew install docker
```

This only install the Docker CLI. This is necessary to interact with the Docker Engine on your Minikube cluster.

Additionaly you need to install hyperkit too:

```
brew install hyperkit
```

Excourse on Hyperkit
"HyperKit is a toolkit for embedding hypervisor capabilities in your application [...], which is optimized for
lightweight virtual machines and container deployment. [...]HyperKit currently only supports macOS using the
Hypervisor.framework. It is a core component of Docker Desktop for Mac."

source: https://github.com/moby/hyperkit

It seems that we already found a good and lightweight solution for our new Docker environment.

Before we start our Minikube cluster, we want to connect our Docker CLI with out "Docker Engine".

```
eval $(minikube docker-env)
```

Finally you can start your Minikube cluster.

```
minikube start --driver=hyperkit --container-runtime=docker --disk-size=50g -p minikube
```

### Helpful commands

We've seen how to start a minikube cluster. We have defined and installed the first parameters (driver &
container-runtime). But we can also define more about the hardware as well. By default, it starts with 2 CPUs and 2GB.
We can define the

- memory
- disk-size
- cpus
  But the last parameter is the abbreviation for profile. This is the name for the cluster. We can have multiple
  profiles with different specifications and clusters.
  The default profile is named minikube and you can check out all with

```
minikube profile list
```

After a while, you want to check the used disk space on the virtual machine. Therefore you need to ssh in your minikube
cluster. You can easily do that with

```
minikube ssh
```

### Good to know

On Docker Desktop I am used to check my apps on the localhost. In this case you need to check out your minikube ip. As
you can imagine, this is quite simple as well

```
minikube ip
```

Often times I get an error, because I forgot to mount a volume. This is a slightly more complicated, but don't worry, it
is not that hard.
You just need to open another terminal and create a mount source and target and let it open, until you are finished with
your Container work.

```
minikube mount /Users/<your-path>/projects/maltequast.github.io:/host
```

In this example I wanted to serve my GitHub blog for testing purposes on my local machine. For this reason I needed to
mount this directory. Now I was able to find it in my minikube cluster in the host directory and was able to serve and
test it.

One more handy feature is the addons list.

```
minikube addons list
```

```
|-----------------------------|----------|--------------|--------------------------------|
|         ADDON NAME          | PROFILE  |    STATUS    |           MAINTAINER           |
|-----------------------------|----------|--------------|--------------------------------|
| ambassador                  | minikube | disabled     | third-party (ambassador)       |
| auto-pause                  | minikube | disabled     | google                         |
| csi-hostpath-driver         | minikube | disabled     | kubernetes                     |
| dashboard                   | minikube | disabled     | kubernetes                     |
| default-storageclass        | minikube | enabled ✅   | kubernetes                     |
| efk                         | minikube | disabled     | third-party (elastic)          |
| freshpod                    | minikube | disabled     | google                         |
| gcp-auth                    | minikube | disabled     | google                         |
| gvisor                      | minikube | disabled     | google                         |
| helm-tiller                 | minikube | disabled     | third-party (helm)             |
| ingress                     | minikube | disabled     | unknown (third-party)          |
| ingress-dns                 | minikube | disabled     | google                         |
| istio                       | minikube | disabled     | third-party (istio)            |
| istio-provisioner           | minikube | disabled     | third-party (istio)            |
| kong                        | minikube | disabled     | third-party (Kong HQ)          |
| kubevirt                    | minikube | disabled     | third-party (kubevirt)         |
| logviewer                   | minikube | disabled     | unknown (third-party)          |
| metallb                     | minikube | disabled     | third-party (metallb)          |
| metrics-server              | minikube | disabled     | kubernetes                     |
| nvidia-driver-installer     | minikube | disabled     | google                         |
| nvidia-gpu-device-plugin    | minikube | disabled     | third-party (nvidia)           |
| olm                         | minikube | disabled     | third-party (operator          |
|                             |          |              | framework)                     |
| pod-security-policy         | minikube | disabled     | unknown (third-party)          |
| portainer                   | minikube | disabled     | portainer.io                   |
| registry                    | minikube | enabled ✅   | google                         |
| registry-aliases            | minikube | disabled     | unknown (third-party)          |
| registry-creds              | minikube | disabled     | third-party (upmc enterprises) |
| storage-provisioner         | minikube | enabled ✅   | google                         |
| storage-provisioner-gluster | minikube | disabled     | unknown (third-party)          |
| volumesnapshots             | minikube | disabled     | kubernetes                     |
|-----------------------------|----------|--------------|--------------------------------|
```

Depending on the usecase, it might be extremely useful to enable some addons.
Especially if you want to get started with MLOps, I recommend to enable the registry addon and you can push your image
to a local registry.

```
minikube addons enable registry
```

At the end when your are done and you want to free some disk space, you can delete the minikube cluster or just stop it and reuse it.

```
#stop the cluster for reuse
minikube stop

# delete the cluster
minikube delete

# delete all clusters
minikube delete --all
```




### Conclusion

I use this setup since 4 weeks and I am positively surprised. It doesn't slow down my Macbook as much as Docker Desktop.
The installation is easy and the learning curve is quite steep. Additionally I was more interested to test kubectl and
helm as well, but this might be worth another blog post.
Nonetheless, I really appreciate the idea of multiple profiles. Not all the time I want to use the same containers and
hardware specification, especially the container list can feel a little overwhelming when running a Kubernetes cluster,
so another profile can make sense.
Bottomline there are many ways to run a Container and Minikube is one of them, but if you are using it for the first
time, then you should probably look at the great Minikube documentation.

Many thanks for reading this blog post and for sure, there are many more useful addons and tips for Minikube. Let me
know if you have any questions or suggestions.

This blog post is inspired by:

- https://dhwaneetbhatt.com/blog/run-docker-without-docker-desktop-on-macos
- https://minikube.sigs.k8s.io/docs/start/
- 