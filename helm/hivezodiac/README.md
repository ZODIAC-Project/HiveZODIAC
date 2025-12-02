# Kubernetes Deployment with Helm

1. Validate template:
```bash
helm lint helm/hivezodiac
helm template hivezodiac helm/hivezodiac --values helm/hivezodiac/values.yaml
```

Save the File: 
```bash
helm template hivezodiac helm/hivezodiac --values helm/hivezodiac/values.yaml > helm/hivezodiac/rendered.yaml
```
2. Load the image into your Kubernetes cluster (minikube used here):

```bash
minikube image load hivezodiac:latest
```

3. Install or upgrade in your cluster:

```bash
helm upgrade --install hivezodiac helm/hivezodiac -n hivezodiac --create-namespace
```
4. Make the service accessible localy (port-forwarding):
```bash 
# get pod name 
POD=$(kubectl get pods -l app=hivezodiac -o jsonpath='{.items[0].metadata.name}' -n hivezodiac)
```
```bash 
kubectl port-forward pod/$POD 1883:1883 -n hivezodiac
```
5. Stop and remove the deploment and all associated resources:
```bash
helm uninstall hivezodiac
```