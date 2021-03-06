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
.
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

# 9.1
```bash
mkdir primary
echo c > primary/cyan
echo m > primary/magenta
echo y > primary/yellow
echo k > primary/black
echo "known as key" >> primary/black
echo blue > favorite

kubectl create configmap colors --from-literal=text=black --from-file=./favorite --from-file=./primary/

kubectl get configmaps colors

kubectl get configmaps colors -o yaml

kubectl create -f simpleshell.yaml

kubectl exec -it shell-demo -- /bin/bash -c 'echo $ilike'

kubectl delete pod shell-demo

vi simpleshell.yaml
# spec:
#   containers:
#   - name: nginx
#     image: nginx
# #    env:
# #    - name: ilike
# #      valueFrom:
# #        configMapKeyRef:
# #          name: colors
# #          key: favorite
#     envFrom:
#     - configMapRef:
#         name: colors

kubectl create -f simpleshell.yaml

kubectl exec -it shell-demo -- /bin/bash -c 'env'

kubectl delete pod shell-demo

kubectl create -f car-map.yaml

kubectl get configmaps fast-car -o yaml

vi simpleshell.yaml
# spec:
#   containers:
#   - name: nginx
#     image: nginx
# ##    env:
# ##    - name: ilike
# ##      valueFrom:
# ##        configMapKeyRef:
# ##          name: colors
# ##          key: favorite
# #    envFrom: 
# #    - configMapRef: 
# #        name: colors
#     volumeMounts:
#     - name: car-vol
#       mountPath: /etc/cars
#   volumes:
#     - name: car-vol
#       configMap:
#         name: fast-car

kubectl create -f simpleshell.yaml

kubectl exec -it shell-demo -- /bin/bash -c 'df -ha | grep car'

kubectl exec -it shell-demo -- /bin/bash -c 'cat /etc/cars/car.trim'

kubectl delete pod shell-demo

kubectl delete configmap fast-car colors
```

# 9.2
## root
```bash
sudo apt-get update && sudo apt-get install -y nfs-kernel-server

sudo mkdir /opt/sfw

sudo chmod 1777 /opt/sfw

sudo bash -c 'echo software > /opt/sfw/hello.txt'

sudo vi /etc/exports
# /opt/sfw/ *(rw,sync,no_root_squash,subtree_check)

sudo exportfs -ra
```

## worker
```
sudo apt-get -y install nfs-common

showmount -e master

sudo mount 192.168.108.160:/opt/sfw /mnt

ls -l /mnt
```

## root
```bash
vi PVol.yaml

kubectl create -f PVol.yaml

kubectl get pv
```

# 9.3
```bash
kubectl get pvc

vi pvc.yaml

kubectl create -f pvc.yaml

kubectl get pvc

kubectl get pv

kubectl create -f nfs-pod.yaml

kubectl get pod

kubectl describe pod nginx-nfs-c96d64f49-smm44

kubectl get pvc
```

# 9.4
```bash
kubectl delete deploy nginx-nfs

kubectl delete pvc pvc-one

kubectl delete pv pvvol-1

kubectl create namespace small

kubectl describe ns small

kubectl -n small create -f PVol.yaml

kubectl -n small create -f pvc.yaml

kubectl -n small create -f storage-quota.yaml

kubectl describe ns small

vi nfs-pod.yaml
# namespace: small

kubectl -n small create -f nfs-pod.yaml

kubectl get deploy --all-namespaces

kubectl -n small describe deploy nginx-nfs

kubectl -n small get pod

kubectl -n small describe pod nginx-nfs-c96d64f49-jk795

kubectl describe ns small

sudo dd if=/dev/zero of=/opt/sfw/bigfile bs=1M count=300

kubectl describe ns small

du -h /opt/

kubectl -n small get deploy

kubectl -n small delete deploy nginx-nfs

kubectl describe ns small

kubectl -n small get pvc

kubectl -n small delete pvc pvc-one

kubectl -n small get pv

kubectl get pv/pvvol-1 -o yaml

kubectl delete pv/pvvol-1

grep Retain PVol.yaml

kubectl create -f PVol.yaml

kubectl patch pv pvvol-1 -p '{"spec":{"persistentVolumeReclaimPolicy": "Delete"}}'

kubectl get pv/pvvol-1

kubectl describe ns small

kubectl -n small create -f pvc.yaml

kubectl describe ns small

kubectl -n small get resourcequota

kubectl -n small delete resourcequotas storagequota

vi storage-quota.yaml
#    requests.storage: "100Mi"

kubectl -n small create -f storage-quota.yaml

kubectl describe ns small

kubectl -n small create -f nfs-pod.yaml

kubectl -n small describe deploy/nginx-nfs

kubectl -n small get po

kubectl -n small delete deploy nginx-nfs

kubectl -n small delete pvc/pvc-one

kubectl -n small get pv

kubectl delete pv/pvvol-1

vi PVol.yaml
#  persistentVolumeReclaimPolicy: Recycle

kubectl -n small create -f low-resource-range.yaml

kubectl describe ns small

kubectl -n small create -f PVol.yaml

kubectl get pv

kubectl -n small create -f pvc.yaml

kubectl -n small edit resourcequotas
#spec:
#  hard:
#    persistentvolumeclaims: "10"
#    requests.storage: 500Mi

kubectl -n small create -f pvc.yaml

kubectl -n small create -f nfs-pod.yaml

kubectl -n small delete deploy nginx-nfs

kubectl -n small get pvc

kubectl -n small get pv

kubectl -n small delete pvc/pvc-one

kubectl -n small get pv

kubectl delete pv/pvvol-1
```

# 10.1
```bash
kubectl create deployment secondapp --image=nginx

kubectl get deployment secondapp -o yaml | grep label -A2

kubectl expose deployment secondapp --type=NodePort --port=80

kubectl create -f ingress.rbac.yaml 

wget https://tinyurl.com/yawpexdt -O traefik-ds.yaml

kubectl create -f traefik-ds.yaml

kubectl create -f ingress.rule.yaml

ip a

curl -H "Host: www.example.com" http://192.168.108.160/

kubectl create deployment thirdpage --image=nginx

kubectl get deployment thirdpage -o yaml | grep -A2 label

kubectl expose deployment thirdpage --type=NodePort --port=80

kubectl exec -it thirdpage-7bd8d45875-jd72t -- /bin/bash

cat > /usr/share/nginx/html/index.html
This is the ingress test!!!!!!!
^d
exit

kubectl edit ingress ingress-test 
#spec:
#  rules:
#  - host: www.example.com
#    http:
#      paths:
#      - backend:
#          serviceName: secondapp
#          servicePort: 80
#        path: /
#  - host: thirdpage.org
#    http:
#      paths:
#      - backend:
#          serviceName: thirdpage
#          servicePort: 80
#        path: /

curl -H "Host: thirdpage.org" http://192.168.108.160/
```

# 11.1
```bash
kubectl get nodes

kubectl describe nodes | grep -i label

kubectl describe nodes | grep -i taint

kubectl get deployments --all-namespaces

## master
sudo docker ps | wc -l
24

## worker
sudo docker ps | wc -l
14

kubectl label nodes master status=vip

kubectl label nodes worker1 status=other

kubectl get nodes --show-labels

kubectl create -f vip.yaml

## master
sudo docker ps | wc -l
29

## worker
sudo docker ps | wc -l
14

kubectl delete pod vip

vi vip.yaml
# #  nodeSelector:
# #    status: vip

kubectl get pods

## master
sudo docker ps | wc -l
24

## worker
sudo docker ps | wc -l
19

cp vip.yaml other.yaml

sed -i s/vip/other/g other.yaml

vi other.yaml
#  nodeSelector:
#    status: other

kubectl create -f other.yaml

## master
sudo docker ps | wc -l
24

## worker
sudo docker ps | wc -l
14

kubectl delete pods vip other

kubectl get pods
```

# 11.2
```bash
kubectl delete deployment secondapp thirdpage

kubectl create -f taint.yaml

sudo docker ps | grep nginx

## master
sudo docker ps | wc -l
26

## worker
sudo docker ps | wc -l
24

kubectl delete deployment taint-deployment

## master
sudo docker ps | wc -l
24

kubectl taint node worker1 bubba=value:PreferNoSchedule

kubectl describe nodes | grep -i taint

kubectl create -f taint.yaml

## master
sudo docker ps | wc -l
32

## worker
sudo docker ps | wc -l
18

kubectl delete deployment taint-deployment

kubectl taint nodes worker1 bubba-

kubectl describe nodes | grep -i taint

kubectl taint node worker1 bubba=value:NoSchedule

kubectl create -f taint.yaml

## master
sudo docker ps | wc -l
40

## worker
sudo docker ps | wc -l
10

kubectl delete deployment taint-deployment

kubectl taint nodes worker1 bubba-

kubectl create -f taint.yaml

## master
sudo docker ps | wc -l
26

## worker
sudo docker ps | wc -l
24

kubectl taint node worker1 bubba=value:NoExecute

## master
sudo docker ps | wc -l
40

## worker
sudo docker ps | wc -l
6

kubectl taint nodes worker1 bubba-

## master
sudo docker ps | wc -l
40

## worker
sudo docker ps | wc -l
10

kubectl get nodes

kubectl drain worker1

kubectl get nodes

kubectl delete deployment taint-deployment

kubectl create -f taint.yaml

## master
sudo docker ps | wc -l
40

kubectl uncordon worker1

kubectl get nodes

## master
sudo docker ps | wc -l
26

kubectl delete deployment taint-deployment
```
