# Установка кластера Kubernetes с региональным мастером

## Поехали!
```
export FOLDER=$(yc resource folder get cameda-practicum --format=json | jq -r ".id")
export ZONE=ru-central1-d
export SA=$(yc iam service-account get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export NETWORK=$(yc vpc network get cameda-practicum --folder-name cameda-practicum --format json | jq -r '.id')
export SUBNET=$(yc vpc subnet get subnet-d --folder-name cameda-practicum --format json | jq -r '.id')
export SG=$(yc vpc sg get k8s-sg --folder-name cameda-practicum --format=json | jq -r '.id')
export KMS=$(yc kms symmetric-key get k8s-key --folder-name cameda-practicum --format json | jq -r ".id")
export LOG=$(yc log group get k8s --folder-name cameda-practicum --format json | jq -r ".id")
export VERSION=1.30
export CHANNEL=rapid # regular, stable
```

### Create cluster with zonal master, cilium and logs.
```
yc k8s cluster create \
--folder-id $FOLDER \
--name cam-kuber-regional-test \
--description "regional k8s test cluster" \
--labels test=regionaal \
--network-id $NETWORK \
--regional \
--master-location subnet-name=subnet-a,zone=ru-central1-a \
--master-location subnet-name=subnet-b,zone=ru-central1-b \
--master-location subnet-name=subnet-d,zone=ru-central1-d \
--public-ip \
--release-channel $CHANNEL \
--version $VERSION  \
--cluster-ipv4-range 10.90.0.0/16 \
--service-ipv4-range 10.91.0.0/16 \
--auto-upgrade=true \
--security-group-ids $SG \
--enable-network-policy \
--cilium \
--node-service-account-id $SA \
--service-account-id $SA \
--kms-key-id $KMS \
--daily-maintenance-window 'start=22:00,duration=2h' \
--master-logging enabled=true,log-group-id=$LogGroupID,cluster-autoscaler-enabled=true,kube-apiserver-enabled=true,audit-enabled=true,events-enabled=true \
--async
```

### Create 2 worker node. Fixed node group.
```
yc k8s node-group create \
--folder-id $FOLDER \
--name cam-fixed-group \
--cluster-name cam-kuber-regional-test \
--description "fixed-testgroup" \
--metadata serial-port-enable=1 \
--metadata-from-file=ssh-keys=/Users/cameda/ssh-pairs.txt \
--labels cam=regional \
--node-labels env=deploy \
--location zone=$ZONE \
--platform standard-v3 \
--memory 16 \
--cores 8 \
--core-fraction 100 \
--disk-type network-ssd \
--disk-size 96 \
--network-acceleration-type standard \
--network-interface security-group-ids=$SG,subnets=$SUBNET,ipv4-address=nat \
--version $VERSION \
--container-runtime containerd \
--fixed-size 2 \
--auto-upgrade=true \
--auto-repair \
--max-expansion 1 \
--max-unavailable 1 \
--daily-maintenance-window 'start=22:00,duration=5h' \
--allowed-unsafe-sysctls net.ipv4.tcp_timestamps \
--async
```

### Create 1-5 worker node. Autoscale node group.
```
yc k8s node-group create \
--folder-id $FOLDER \
--name cam-autoscale-group \
--cluster-name cam-kuber-regional-test \
--description "autoscale-testgroup" \
--metadata serial-port-enable=1 \
--metadata-from-file=ssh-keys=/Users/cameda/ssh-pairs.txt \
--labels cam=autoscale \
--node-labels env=test \
--location zone=$ZONE \
--platform standard-v3 \
--memory 4 \
--cores 2 \
--core-fraction 100 \
--disk-type network-ssd \
--disk-size 96 \
--network-acceleration-type standard \
--network-interface security-group-ids=$SG,subnets=$SUBNET,ipv4-address=nat \
--version $VERSION \
--container-runtime containerd \
--auto-scale min=1,max=5,initial=1 \
--auto-upgrade=false \
--auto-repair \
--max-expansion 1 \
--max-unavailable 1 \
--weekly-maintenance-window 'days=[monday,tuesday],start=22:00,duration=10h' \
--allowed-unsafe-sysctls net.ipv4.tcp_timestamps \
--async
```

### Содержимое файла ssh-pairs.txt
```
cameda:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDI98mJDBN9cnp6HOdBYTQILeAhUSDvDfoqA9iLmVPDyPLFRWs7tE4BjCAcFD6a3M50QIboCaohfa7h+PWksYibab7I3QHOR7y9pCW8FGonGRw2ACvt906qlaWHFj7jWOxuihFoiRROKqLCW5YE/Yc4XFIvW1gu3JQdvQ1wemWvujsI8EHE6PI1pEg7/41y6kn3IhNHIr8WRLe4dPyPGjwc4LpBCcaRSJiX4YjVXynSIHNk365UrL+nGv8ix7bW5FNCgGqSgfUTVCfMYLzQ/gYHPVQrcIvCeHjkwluH8Z3gXeN3OliejBjpLi+IWIzd9K6UADSUNU8oL+9941tDidp8APoe7RbB4h3bY6k8Bhy0yxohgQS2OWSYd1mjeEx8Ba5wzJKqfpUgmcPdrBJnBwLgLMFQyEfYG6vTPkYWAKEvkkJ6ZiA4tdoQvCb+B0xJV/ivHyLtoi3LFE59mbQFDUy8O51vX9JjBDLwzyTEeslWp7uOP66Ti5Q5ucNXbs5yXTU= cameda@cameda-osx
```

### Подключение к кластеру.
```
yc managed-kubernetes cluster get-credentials --id <cluster_id> --external
```
