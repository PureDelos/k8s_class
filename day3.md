Day 3
=====

# 8.1
```bash
kubectl get nodes --show-labels

kubectl create -f nginx-one.yaml

kubectl create ns accounting

kubectl create -f nginx-one.yaml

kubectl -n accounting get po

kubectl -n accounting describe po nginx-one-54479f7c5c-bh7b8

kubectl label node worker1 system=secondOne

kubectl get nodes --show-labels

kubectl -n accounting get po

kubectl get pods -l app=nginx --all-namespaces 

kubectl -n accounting expose deployment nginx-one

kubectl -n accounting get ep nginx-one

kubectl -n accounting delete deploy nginx-one

vi nginx-one.yaml 
#         - containerPort: 80 <= change

kubectl create -f nginx-one.yaml
```

# 8.2
```bash
kubectl -n accounting expose deployment nginx-one --type=NodePort --name=service-lab

kubectl -n accounting describe svc

kubectl cluster-info

curl http://192.168.108.160:32149
```

# 8.3
```bash
kubectl delete pod -l app=nginx --all-namespace

kubectl -n accounting delete pod -l app=nginx

kubectl -n accounting get pods

kubectl -n accounting get deployments --show-labels

kubectl -n accounting delete deploy -l system=secondary

kubectl label node worker1 system-
```
