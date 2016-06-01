## Usage
### Create a GKE cluster

Create a GKE cluster as per Google Container Engine docs. Pull down the kubeconfig and all credentials.  
Setup a separate service account for docker registry and pull down its json key

### Install Kubernetes Helm

Build Helm from source, as described in https://github.com/kubernetes/helm  
Run

```
helm init
```

### Create required Kubernetes secrets

Create the three secrets:

````
kubectl create secret generic gce-key-secret --from-file=keyfile=<PATH TO YOUR DOCKER REGISTRY SERVICE ACCOUNT KEY> --namespace=helm
kubectl create secret generic registry-http-secret --from-literal=secret=<SOME RANDOM STRING OF CHARACTERS> --namespace=helm
kubectl create secret generic kubeconfig-secret --from-file=config=<PATH TO YOUR KUBECONFIG FILE> --namespace=helm
````

### Install registry chart

Install the chart by running 

```
cd pkg/registry_helm
helm install .
```

### Wait for cluster external IP to populate

Run

```
kubectl get service docker-registry --namespace=helm --watch
```

and wait for EXTERNAL-IP to fill out. 


### Make sure your docker daemon can talk to an insecure registry

Create a docker-machine instance with 
```
'--engine-insecure-registry "<EXTERNAL-IP OF REGISTRY SERVICE>:5000"' 
```
parameter. If you are not using docker machine, just modify your docker daemon setting with the same switch, and restart it.

### Build gocd server and agent images and push them to the registry

Go to build/gocd/agent and
```
docker build -t <EXTERNAL-IP OF REGISTRY SERVICE>:5000/gocd-agent .
docker push <EXTERNAL-IP OF REGISTRY SERVICE>:5000/gocd-agent
```

Then, go to build/gocd/server and

```
docker build -t <EXTERNAL-IP OF REGISTRY SERVICE>:5000/gocd-server .
docker push <EXTERNAL-IP OF REGISTRY SERVICE>:5000/gocd-server
```

### Create TOML values for gocd chart

Create a values.toml file somewhere with the following line in it:

*NOTE the CLUSTER-IP, not EXTERNAL-IP here*

```
privateRegistry = "<CLUSTER-IP OF REGISTRY SERVICE>:5000"
```

### Deploy gocd chart
```
cd pkg/gocd_helm
helm install . --values <PATH TO TOML VALUES FILE>
```

### Check out the gocd installation

Once the external ip of gocd kubernetes service populates you should be able to visit 

```
http://<EXTERNAL IP OF GOCD SERVICE>:8153/
```

and trigger a "pipeline" - which will end up using docker-in-docker to build and tag containers, and kubectl to interact with the kubernetes cluster it is running in.