# PDB example
```
cat <<EOF | kubectl apply -f -
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb-available
  namespace: default
  labels: 
    pdb: nginx-prod
  annotations:
    author: cameda
spec:
  minAvailable: 3
  selector:
    matchLabels:
      app: nginx
EOF
```
