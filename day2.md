Day 2
=====

# 4.1

```bash
kubectl create deployment hog --image=vish/stress

kubectl get deployment

kubectl describe deployment hog

kubectl get deployment hog -o yaml

kubectl get deployment hog --export -o yaml > hog.yaml

vi hog.yaml
#        name: stress
#        resources: 
#          limits: 
#            memory: "2Gi"
#          requests: 
#            memory: "1000Mi"

kubectl replace -f hog.yaml

kubectl get deployment hog -o yaml

kubectl get po

kubectl logs hog-6c57fd8784-6w7dj
#I0312 04:11:53.757707       1 main.go:26] Allocating "0" memory, in "4Ki" chunks, with a 1ms sleep between allocations
#I0312 04:11:53.757774       1 main.go:29] Allocated "0" memory

vi hog.yaml
#        resources:
#          limits:
#            cpu: "1"
#            memory: "2Gi"
#          requests:
#            cpu: "0.5"
#            memory: "500Mi"
#        args:
#        - -cpus
#        - "2"
#        - -mem-total
#        - "950Mi"
#        - -mem-alloc-size
#        - "100Mi"
#        - -mem-alloc-sleep
#        - "1s"

kubectl delete deployment hog

kubectl create -f hog.yaml

```

# 4.2

```bash
kubectl create namespace low-usage-limit

kubectl get namespace

vi low-resource-range.yaml
#apiVersion: v1
#kind: LimitRange
#metadata:
#  name: low-resource-range
#spec:
#  limits:
#  - default:
#      cpu: 1
#      memory: 500Mi
#    defaultRequest:
#      cpu: 0.5
#      memory: 100Mi
#    type: Container

kubectl --namespace=low-usage-limit create -f low-resource-range.yaml

kubectl get limitrange

kubectl get limitrange --all-namespaces

kubectl -n low-usage-limit create deployment limited-hog --image=vish/stress

kubectl get deployment --all-namespaces

kubectl -n low-usage-limit get pods

kubectl -n low-usage-limit get pod limited-hog-568fbbf4cf-6fp52 -o yaml

cp hog.yaml hog2.yaml

vi hog2.yaml
#  labels:
#    app: hog
#  name: hog
#  namespace: low-usage-limit         <- Add
#  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/hog

kubectl create -f hog2.yaml

kubectl get deployment --all-namespaces

kubectl -n low-usage-limit delete deployment hog

kubectl delete deployment hog
```

# 4.3

```bash
kubectl create namespace sock-shop

kubectl get namespace

grep image complete-demo.yaml 
#        image: mongo
#        image: weaveworksdemos/carts:0.4.8
#        image: weaveworksdemos/catalogue-db:0.3.0
#        image: weaveworksdemos/catalogue:0.3.5
#        image: weaveworksdemos/front-end:0.3.12
#        image: mongo
#        image: weaveworksdemos/orders:0.4.7
#        image: weaveworksdemos/payment:0.4.3
#        image: weaveworksdemos/queue-master:0.3.1
#        image: rabbitmq:3.6.8
#        image: weaveworksdemos/shipping:0.4.8
#        image: weaveworksdemos/user-db:0.4.0
#        image: weaveworksdemos/user:0.4.4

kubectl apply -n sock-shop -f complete-demo.yaml

kubectl get pods

kubectl get pods -n sock-shop

kubectl get svc -n sock-shop

kubectl get deployment --all-namespaces

kubectl get nodes

kubectl drain worker1 

kubectl describe nodes | grep -i taint

kubectl drain worker1 --ignore-daemonsets

kubectl drain worker1 --ignore-daemonsets --delete-local-data

kubectl uncordon worker1

kubectl describe nodes | grep -i taint

kubectl -n sock-shop get pod

kubectl -n sock-shop delete pod catalogue-6759fc9bf5-5zf67 catalogue-db-99cbcbb88-r7zc2 front-end-77b48955b6-7bmnw orders-db-86f5c494b9-rnznr orders-b56f44784-qrzt9

kubectl -n sock-shop get pod

kubectl -n sock-shop delete deployment catalogue catalogue-db front-end orders

kubectl -n sock-shop get pod | grep catalogue

kubectl -n sock-shop get deployment

kubectl delete -f complete-demo.yaml
```
