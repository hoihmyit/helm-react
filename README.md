🚀 Deploy a sample React app using Helm + Podman Desktop + Kind cluster

## Create a new cluster
```
kind create cluster --name <cluster-name>
```

## Build the React app image
```
podman build -t react-app:latest ./react-app
```

## Push your image to a container registry
### Tag your local image for a registry you have access to (Docker Hub, private registry, etc.):
```
podman tag react-app:latest hoihmydocker/react-app:latest
```

### Log in to that registry:
```
podman login docker.io
# enter your credentials
```

### Push the image:
```
podman push hoihmydocker/react-app:latest
```

## Install the Helm chart
```
helm install react-app ./chart
```

## Upgrade the Helm chart if needed
```
helm upgrade react-app ./chart
```

## Uninstall the Helm release
```
helm uninstall react-app
```

## Stop a pod temporarily (a pod stop, deployment remains)
```
kubectl scale deployment <deployment-name> --replicas=0
```

## Get the pod name
```
kubectl get pods --namespace default -l "app.kubernetes.io/name=react-app,app.kubernetes.io/instance=react-app" -o jsonpath="{.items[0].metadata.name}"
```

## Get the container port of the pod
```
kubectl get pod --namespace default <pod-name> -o jsonpath="{.spec.containers[0].ports[0].containerPort}"
```

## Access the pod
```
kubectl exec -it <pod-name> -- /bin/sh
```

## Check the deployment status
```
kubectl get pods
kubectl get svc
```

## Forward port to access in browser
```
kubectl --namespace default port-forward <pod-name> 8080:<container-pod:80>
```

## Access the React app via
```
http://localhost:8080/
```

## Use ingress to expose public url, port

