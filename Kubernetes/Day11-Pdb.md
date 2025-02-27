Here's a simple demo of how to use a Pod Disruption Budget (PDB) with a basic deployment in Kubernetes. This ensures that a minimum number of pods remain available during voluntary disruptions (like draining a node).

1. Create a Deployment
We will create a simple nginx deployment with 3 replicas.

```apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

2. Define a Pod Disruption Budget (PDB)
A PDB ensures at least one pod remains available during voluntary disruptions.
```
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app: nginx
```
3. Apply the Resources
Save the above YAML files and apply them:
```
kubectl apply -f deployment.yaml
kubectl apply -f pdb.yaml
```
4. Verify PDB
Check the PDB status:

```
kubectl get pdb
```
