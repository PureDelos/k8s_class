홍혜선
010-5446-1412
rosehs00@nate.com / rosehs@gkn.co.kr

-----

CKA - Ubuntu 기반

-----

# 사전 세팅 #
## OS 계정 ##
* student / student
* root / abc123

## NW ##
* subnet : 192.168.108.0/24
* gateway : 192.168.108.2

## static ip 설정 ##
``` bash
vi /etc/network/interfaces

# dhcp->static
# iface ens33 inet static
# address 192.168.108.160
# netmask 255.255.255.0
# broadcast 192.168.108.255
# gateway 192.168.108.2
# dns-nameservers 192.168.108.2
```

## vi ## 
* cw(word change)
* x(한문자 삭제)
* u(undo)

## xshell 설정 ##

## /etc/hosts ## 
```bash
vi /etc/hosts

# 192.168.108.160 master
# 192.168.108.161 worker1
```

## swap disable ##
```bash
vi /etc/fstab
# 주석처리 > UUID=71fd7508-6dc0-4a32-8e3b-8a3b3bb60cfb none            swap    sw              0       0

# 확인 : swapon -s => 출력 없음
```

## sudoer ##
* sudo group member 확인
```bash
id -a student

# uid=1000(student) gid=1000(student) groups=1000(student),4(adm),24(cdrom),**27(sudo)**,30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare)

vi /etc/sudoers
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL
```

-----

# Solution download #
```bash
wget --user=LFtr****** --password=Pen******** https://training.linuxfoundation.org/cm/LFS458/LFS458_V1.13.1-u1_SOLUTIONS.tar.bz2
tar -xvf LFS458_V1.13.1-u1_SOLUTIONS.tar.bz2
cp LFS458/SOLUTIONS/*/* ~student/lab
cp -r LFS458/SOLUTIONS/s_04 ~student/lab
cp -r LFS458/SOLUTIONS/s_16 ~student/lab
```

-----

# 설치 #
## master ##
### root ###
```bash
apt-get update && apt-get upgrade -y

apt-get install -y docker.io

vim /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

apt-get update

apt-get install -y kubeadm=1.13.1-00 kubelet=1.13.1-00 kubectl=1.13.1-00

wget https://tinyurl.com/yb4xturm -O rbac-kdd.yaml
wget https://tinyurl.com/y8lvqc9g -O calico.yaml

grep -i cidr -A 5 calico.yaml

kubeadm init --kubernetes-version 1.13.1 --pod-network-cidr 192.168.0.0/16 | tee kubeadm-init.out

###
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 192.168.108.160:6443 --token 7l25fl.sqj1m2bcjkxlaw0b --discovery-token-ca-cert-hash sha256:30592907ea58f37e5e9c96688180f6ffc534751cd56eecc34ca9048e209d983c
###
```
### student ###
```bash
mkdir -p ~/.kube
sudo cp -i /etc/kubernetes/admin.conf ~/.kube/config
sudo chown student:student ~/.kube/config

sudo cp /root/rbac-kdd.yaml .
kubectl apply -f rbac-kdd.yaml

sudo cp /root/calico.yaml .
kubectl apply -f calico.yaml

source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

## worker ##
### root ###
```bash
apt-get update && apt-get upgrade -y

apt-get install -y docker.io

vim /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

apt-get update

apt-get install -y kubeadm=1.13.1-00 kubelet=1.13.1-00 kubectl=1.13.1-00

kubeadm join 192.168.108.160:6443 --token 7l25fl.sqj1m2bcjkxlaw0b --discovery-token-ca-cert-hash sha256:30592907ea58f37e5e9c96688180f6ffc534751cd56eecc34ca9048e209d983c
```

## master ##
### student ###
```bash
kubectl describe node | grep -i taint

kubectl taint nodes --all node-role.kubernetes.io/master-
```

# 오브젝트 생성 확인 #
```bash
# nginx deployment 생성

kubectl create deployment nginx --image=nginx

kubectl describe deployments nginx

kubectl get events

# nginx deployment yaml 출력
kubectl get deployment nginx -o yaml

kubectl get deployment nginx -o yaml > first.yaml

kubectl delete deployment nginx

kubectl create -f first.yaml

kubectl get deployment nginx -o yaml > second.yaml

diff first.yaml second.yaml

kubectl create deployment two --image=nginx --dry-run -o yaml

kubectl get deployment

kubectl get deployments nginx --export -o yaml

kubectl get deployments nginx --export -o json

# nginx deployment service 생성
kubectl expose -h

kubectl expose deployment/nginx

kubectl replace -f first.yaml

kubectl get deploy,pod

kubectl expose deployment/nginx

kubectl get svc nginx

kubectl get ep nginx

kubectl describe pod nginx-7db75b8b78-n9q2q | grep Node:
```

## worker ##
```bash
# tcp 모니터링
sudo tcpdump -i tunl0
```

## master ##
```bash
# tcp 요청 확인
curl 10.110.61.185:80

curl 192.168.108.161:80

kubectl get deployment nginx

# nginx deployment scale out(replica 3)
kubectl scale deployment nginx --replicas=3

kubectl get deployment nginx

kubectl get ep nginx

kubectl get po -o wide

kubectl delete po nginx-7db75b8b78-frgjq

kubectl get po

kubectl get ep nginx

curl 10.110.61.185:80

kubectl get po

kubectl exec nginx-7db75b8b78-hlh2g -- printenv | grep KUBERNETES

kubectl get svc

kubectl delete svc nginx

# nginx deployment LoadBalancer type service 생성
kubectl expose deployment nginx --type=LoadBalancer

kubectl get svc

kubectl expose deployment nginx --type=NodePort

# nginx deployment scale in(replica 0)
kubectl scale deployment nginx --replicas=0

kubectl get po

# nginx deployment scale out(replica 2)
kubectl scale deployment nginx --replicas=2

kubectl get po

kubectl delete deployments nginx

kubectl delete ep nginx

kubectl delete svc nginx
```
