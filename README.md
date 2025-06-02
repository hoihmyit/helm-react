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

## Expose Ingress Outside the Kind Cluster
1. Create a new file called kind-config.yaml to increase the resource
2. Install Ingress Controller (e.g., NGINX)
```aiignore
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/kind/deploy.yaml
```
3. Verify the NGINX Ingress Controller
```
kubectl get pods -n ingress-nginx
```
4. Recreate the cluster
```
kind delete cluster
kind create cluster --config kind-config.yaml
```
5. Enable Ingress and update the values.yaml
```
service:
  targetPort: 80
...
ingress:
  enabled: true
  className: nginx
  annotations:
     kubernetes.io/ingress.class: nginx
  hosts:
    - host: localhost
      paths:
        - path: /
          pathType: ImplementationSpecific
```
6. Update the targetPort in the service.yaml file
```
spec:
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
```
7. Update the containerPort in the deployment.yaml file
```
containers:
  ports:
    - name: http
      containerPort: {{ .Values.service.targetPort }}
```
8. Uninstall and install the Helm
```
helm uninstall react-app
helm install react-app ./chart
```
or
```
helm upgrade --install react-app ./chart
```
9. Access to React app via
```
http://localhost:8080/
```

## Other
1. Check Cluster Nodes
```
kubectl get nodes
```
2. Describe Node for Detailed Resources
```
kubectl describe node <node-name>
```
3. Check Node Resource Usage (Metrics Server)
```
kubectl top nodes
```
- If you don’t have metrics-server, you can install it:
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
4. Check if the pod is running and ready
```
kubectl get pods -o wide
```
5. Exec into the pod to troubleshoot if needed
```
kubectl exec -it <pod-name> -- /bin/sh
```

