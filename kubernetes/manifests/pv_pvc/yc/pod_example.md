# Пример пода

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: cam-pod-busybox
  namespace: default
  labels:
    cam: busybox
  annotations:
    author: cameda
spec:
  containers:
  - name: busybox
    image: busybox
    imagePullPolicy: IfNotPresent
    resources:
      requests:
        cpu: "100m"
        memory: "100Mi"
      limits:
        memory: "120Mi"
    command: ["sh", "-c"]
    args: ["sleep 3650d"]
    volumeMounts:
    - mountPath: "/User/worker/data"
      name: my-csi
  restartPolicy: OnFailure
  hostname: busybox
  nodeSelector:
    kubernetes.io/os: linux
  volumes:
  - name: my-csi
    persistentVolumeClaim:
      claimName: pvc-dynamic-ssd
EOF
```
--------------------------------------------------------
```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: new-pod
spec:
  containers:
  - name: new-container
    image: cr.yandex/crp59ldu2qv9q43uq5jh/debian:latest
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - mountPath: "/User/worker/data"
      name: my-csi
  volumes:
  - name: my-csi
    persistentVolumeClaim:
      claimName: pvc-dynamic-ssd
EOF
```
