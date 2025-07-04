
# Kubectl Commands

## kubectl get

```bash
# It will return all the running pods
kubectl get pods

# It will retrun all the pods with details
kubectl get pods -o wide

# It will return all the services
kubectl get svc

# It will return all the services with details
kubectl get svc -o wide

# It will retrun all the deployments
kubectl get deployments

# It will retrun all the deployments with details
kubectl get deployments -o wide
```
## kubectl run

```bash
# It will create a new pod
kubectl run pod <name of the pod> --image=<image name>
eg: kubectl run pod nginx --image=nginx
```

## kubectl create
```bash
# It will create the deployment
kubectl create deployment <name of the deployment> --image=<image name>
eg: kubectl create deployment nginx --image=nginx
```

## kubectl expose
```bash
# It will create the service which will expose the pod on internet
kubectl expose pod <pod name> --port=<port-number> --type=<port-type>
eg: kubectl expose pod nginx --port:80 --type=NodePort
```

## kubectl delete
```bash
# It will delete the pod, deployment and service
kubectl delete pod <pod name>
kubectl delete service <service name>
kubectl delete deployment <deployment name>
```
