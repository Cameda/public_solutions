# PDB example
```
cat <<EOF | kubectl apply -f -
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb-unavailable
  namespace: default
  labels:
    pdb: nginx-prod
  annotations:
    author: cameda
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx
EOF
```
