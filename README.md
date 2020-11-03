# monitoring-the-easy-way

This repository includes all the content needed to make the workshop of the same name, the main goal is to have a working environment to learn an practices monitoring concepts.

The tools needed to deploy and use this repository on the next list:

* Docker Desktop (https://www.docker.com/products/docker-desktop)
* Helm 3 (https://helm.sh/docs/intro/quickstart/)
* Your favorite web browser
* Your favorite text editor

# Setup local environment

## Configure Docker Desktop

All the workshop is based on the usage of [Docker](https://www.docker.com/) containers and for explaining better the concepts we will use [Kuberentes](https://kubernetes.io/),
in order to not needing an online deployment on a cloud provider, we need to activate the Kubernetes feature included on Docker Desktop.

First, you have to install Docker Desktop following the [documentation](https://www.docker.com/products/docker-desktop)

You only need to access the Docker preferences and navigate to the Kubernetes section to activate. It will take some minutes to be ready depending on your computer and internet bandwidth.

## Configure Helm

When the local Kubernetes context is ready you can start with the Helm configuration to have your environment ready.

At the moment of preparing this workshop, the current Helm version is 3 and because of that, all the steps are based on that specific version.

To install Helm as a local command you should follow the [quickstart](https://helm.sh/docs/intro/quickstart/) documentation and add at least one repository to start installing charts.

```bash
helm repo add stable https://charts.helm.sh/stable
```

### Extra repositories

AS a more complete environment 4 more repositories can be added to have acces to more charts:

```bash
helm repo add kiwigrid https://kiwigrid.github.io
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add prometheus-comunity https://prometheus-community.github.io/helm-charts
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

Don't forget to update your repository list

```bash
helm repo update
```

# Installing the tools

First we need to create a namespace.

```bash
kubectl create monitoring-easy
```
And set the new namespaces the current context.

```bash
kubectl config set-context --current --namespace=monitoring-easy
```

## Install ingress controller

To make the access more ease we have prepared a basic ingress-controller using the comunity version of Nginx ingress controller, you can install it localy using next command:

```bash
helm install proxy proxy
```

## Install grafana and prometheus

In order to install the tools needed you can simply run next commend to install the chart that aready includes Grafana and Prometheus as dependencies and will leave the cluster ready
to start colecting and showing data.

```bash
cd monitoring-stack
helm dependency update
helm install monitoring-stack .
```

You can now see how the diferent services are created and starts creating the pods needed. When all services are on running state you can connect to your grafana for the first time and you need
to run next commands to obtain the right port to connect and the random admin password generated during the installation process.

```bash
kubectl get secrets monitoring-stack-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

```bash
export GRAFANA_PORT=$(kubectl get service -o jsonpath="{.spec.ports[0].nodePort}" monitoring-stack-grafana)
echo http://localhost:$GRAFANA_PORT
```

# Install example service

This repository includes a basic server example that can be used to generate metrics and test the environment. You can install using next command:

```bash
helm install lab service-example
```

# Install artillery

To demonstrate the different metris a benchmark tool is needed, one easy way to perform a load test is to use [artillery](https://artillery.io/) you can install it by running next command:

```bash
sudo npm install -g artillery --allow-root --unsafe-perm=true
```
