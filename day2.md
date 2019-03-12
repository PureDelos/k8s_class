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

# 5.1
```bash 
export client=$(grep client-cert ~/.kube/config | cut -d" " -f 6)

echo $client
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM4akNDQWRxZ0F3SUJBZ0lJZVkwcmxYei9JeUV3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB4T1RBek1URXdOekV3TVRkYUZ3MHlNREF6TVRBd056RXdNalJhTURReApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sa3dGd1lEVlFRREV4QnJkV0psY201bGRHVnpMV0ZrCmJXbHVNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDQVFFQW1sV2xRZkUyTVlvcDZJSEYKUXh0alplQkpqZXJWZC9TK2ZqbGowUytra1F2aDZWWGd6Sm9Xd1ZhRlZCK3p6dnlSMXduYTRwVzJnMDJaQkFqZgoyS3g2NTMyckpQR2JLWjZkMU5LcUJRQlEwZlY4WEFKc0JDeDhzdHJZYlV3M3B6MlZZZlRyY1VvZ2ZjS2V6U08rCm1iaGRwUU9NQkt1U1V3UC9lZWQ0NWpYaDVHenVRdGorN2E5dGMwc3EvNkk3bHdWTHBYaTJ1MXZLTU9RK21Wc24KWEdNUWQ4TDVaSG9EMXZ5ejdmNGRKbUtnN0lBaGprbW1wRWs2V010a0J2NTdBb2ZaSGo1Nm55N2RpTmJyRWdjSApRZXk4Y2xkN0s1cXd6TjNrWmJzM01pQmJzS3BHRitQaE9yeEtHcTJFR0pMKzR6UHpCNUZ1QW9KL2p2RG9nRU4yCkplMjgxd0lEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEVlIwbEJBd3dDZ1lJS3dZQkJRVUgKQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFMSi9pZzU1U0VFWThyempxS0lUQnlsVG82RmRCODltdmZDRwpZcWp1RDZ3bituTTlMOUhLTXBjQ1JiNDlKQVNkYzBYV2dYQ0I2OHV1KzMyL3U3R1hZQUlIYzVJQUN3djUzTE1SCmhtWWdxaklqSXhORXVMS2ZIWVRVUjFYTysrZ2I2eEMzUk1VMTh1OVE0T2JsKzdpVHRwc2kvc0daRkdOMVJpT2EKWmk1WXdhcjNLNlhqcU5tMks4MGdzelN1SFRkTitrbEd6RWUvejErQnRRTk5SSjFnVXphZEx3ZHkrcStlZTJ5awpla0h1SGdVd0RjRUlXZitBQmNyWWVCaEo5UmxZL3F2UWh6ejZDZGU3NGZnUlVadGZuVmRvRVZHV25jdmFidDdRCm9NNFpOWnlkTWVGYzhQK1JJSHJzRHVHKzIyMGJjemFvbm9FZE8rNy8wMmZBak9wUXR2TT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=

export key=$(grep client-key-data ~/.kube/config | cut -d" " -f 6)
echo $key
LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBbWxXbFFmRTJNWW9wNklIRlF4dGpaZUJKamVyVmQvUytmamxqMFMra2tRdmg2VlhnCnpKb1d3VmFGVkIrenp2eVIxd25hNHBXMmcwMlpCQWpmMkt4NjUzMnJKUEdiS1o2ZDFOS3FCUUJRMGZWOFhBSnMKQkN4OHN0clliVXczcHoyVllmVHJjVW9nZmNLZXpTTyttYmhkcFFPTUJLdVNVd1AvZWVkNDVqWGg1R3p1UXRqKwo3YTl0YzBzcS82STdsd1ZMcFhpMnUxdktNT1ErbVZzblhHTVFkOEw1WkhvRDF2eXo3ZjRkSm1LZzdJQWhqa21tCnBFazZXTXRrQnY1N0FvZlpIajU2bnk3ZGlOYnJFZ2NIUWV5OGNsZDdLNXF3ek4za1piczNNaUJic0twR0YrUGgKT3J4S0dxMkVHSkwrNHpQekI1RnVBb0ovanZEb2dFTjJKZTI4MXdJREFRQUJBb0lCQUJ5allkdi9wMysvMUpENQphNkpOTmIrVXcvRmFyeXZvTldUMHYwbjAza093QWNhcmtlQkZnNDF5d2FEZmxSMEdqd1ZwSmIyLzdETW5OZ3FpCm51NzA2b1dFTXpyU3Zta1ZydEhzR1hKK0lZRWtYV1F4YXR2SGFZaEN4Y0JhVVVWdVR3YnpUTEVrQVMxMDdNVEMKS2o4YUQvNXJ6eEtheDdjeDJibEVNNUg4VTZOd0xXcXJjWTVrM3dpL3g0TTMvdE9SWU4wRHhPNUxEOGIxaTlRQgpocm53c1VXRWY4NEdqUVk3Z1czUGxkZlR3MHRQbTZlUmVxSmxnWDVoM2FTUTZPdXo5WDQ3dFpMdHF4K0U1bkkzCmlQTTZITGkxUlp4UzVVeWI2UFRFOVhYN2V6Q1hWZS83K09ZeFRKWGhTa3dWSVd4cURRS0orK2FzTFEzMGRBaFYKMG9XdXNOa0NnWUVBeWpGc2p4L3hZWnJpOXFBSGNjY2NmYUtDR0ZJN0RubVIwQXZEZG5NTmxFREg4YmdTSG1JbwpKUmNUQkFrSGVxNlFJSGdsTUUzWFBTaUpMVkRwaGtDdmszYkY0VnBnT3drYUVTVW1hbWt2dEVuRWRmMGdCYVNVCnhPSERWSmZGeW4rTDhOOENaWFdlNXptRzF0aW5jckYrMkpIUW9DbWdXckQwRC9QT3MxY2hvYk1DZ1lFQXcyZlIKeFdFR3lnYjBXeURSOWplNlprT3ozaGFieGpoV0Rwek9ETlMzWGhEQnlTbUx4QmhpZUI1eThtUzBzb1lKOWZKbApLeUFjSlZCYWpFdU5lN2tCaHhqdVllNy9vK0puSEpkT1RpODIxVjNQNmUzZEFWbWxSYVU3QXZyLzZ4bEhIbENRCm9TSEJjTXBWb3ppN2xVRnhpc1p3L2FrNDlyTWpicldNN2k3ZWZrMENnWUVBd1JFdW13QWlhbFFPa3ZhK0JRdmUKamF6R1V0ajZZVmorUGMxdHlFWVdXbEQ5V3plcnZXMTI3ZXU1a2FuWmhYRDRXTGpBc2Y0eUg4ajhLOVJPR0k3ZAoySTZhWnhQNFBZYjBhQmkwTlBuWnZtcU4rU2hLRW5sVVFTZGVjQUU0c2FMWENwcTMzQS9UT3ZGNGF1Q1lDL0dtCnNMK1RtY3dGdlhPb3FTN2lXZWRDU3ZjQ2dZQlZLZU5vSmdDQ2ZvTnpVQnVTTnZtYlpuaDNHOTFxaDlVaDZ6OTIKb2lNRThVSzBQTkk0ZGZROHEvQ25Lak1DOTU1UnZnSlB5Ri9iOTJodmF1SlFBUExrZ1g0cjJyRTZLUXVOajNoRwpaUmQ5NkxRY0hWcE1JMWovd0tLMml0U05EMmhLa3d4bDNjTmtPQnNZMXpvU05BS2JYQitVdm5NZ09qVUFKRW8yCjZPQjVwUUtCZ1FDOG02aXZoOFRSVEl5OGhwZWJsRm44V05NaklxUXZXU0hGdG9GeGxHc0FzSnZCSGVTeVpQdDAKTVNIbk9HWlNZRDFRbnhHZVBhQnVvZHQ3VTZidHVkVXd2V1hvNFZNNjZ1b2V0c2txTnBka29SNlM2QlNONkFDSgoyclNLS1lMRXdYTUxHcklkTndTdFJjT04vYTQrWk5KSitxczFNR2k5amxFVVRETkhjSWY5Q3c9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

export auth=$(grep certificate-authority-data ~/.kube/config | cut -d" " -f 6)
echo $auth
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRFNU1ETXhNVEEzTVRBeE4xb1hEVEk1TURNd09EQTNNVEF4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTm03Cm92V0JaMDd5NldtMnBJckZuK2FZUVoybEhlZVNlcExTdU9ScUJqSEx4eG14UEI0L09jbms4YXowb3d5ZHBzMVkKdyswT1BrVGZqQWJtQVQ4QmdzQ3dLZTVIYUU2QWtVcEV3ZW5DbUo1emtrd0tSbTdJbkJZRkFUWTlaTno1V0ZLUApCQzV3YXkzWlllNk9lVGRxblZtcUFCQlhucEprSW4za0dzSXVqMXhLNVJITm5RcTZWTHo4eEtWa3hOQTZDd3pRCitiNFRYeUExa2NxOHVFZGVuL0xvenA2TW5MdnJHcHVGM1BBV3dWVHNNdDJES1k3YWFzRlhzL0lGWlNrTitZbS8KSjdUTUJhb0dmdmdhWndKQjN5S2ZVNlRHWE4raWQzQlRuL1U3MmZPY0trN1ZzbDNNNzFXbE5iQ2E5cTY0aTdNZgpWd1ZlZ0MwdnhBaVZqSjU2SEowQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFLOHhDbEl0QzBzTWdEMzRGWVRYejR2ZDdOVHYKblVhU3UySGZIWlZBNld3N0V5Y1BvMGZOeWtDclNadWFGOWtoWXNKcU01bUNSek1uTGt4Nkh1bTQrdWxjc25FUgpTejlXLzNNMmU2ZUZUK2o1VFVNcWtrVzhYcnc2VTlvZzMwbG5JMVBQaXcwNGQxTGYra3JvSDQrUFA4MFc3SmNoCm9PZ3IrVmcxS0ZKQmcvTXh1a1pyTHJObkFNY3hDWks5Szg4bUJLRWxEK0E0bzUxRHhsSjBaRThwMlREaWVMSEoKb0tNa0EwK0FsTkd3dnQvcWFDSG9rTFdtaFlkUVlSTEp0b1ROdzJVQWVEZ0hGbFJNVVR6UURObzBjWGRKMDg1TQp2U0dWNzVnUlg4b1dZWHNEM2FzZkxFUkZyeHBXb3Zsa24wdG5EeVlHdW5nN2hwYWNvczRUczFFYXZSVT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=

echo $client | base64 -d - > ./client.pem
echo $key | base64 -d - > ./client-key.pem
echo $auth | base64 -d - > ./ca.pem


kubectl config view | grep server
    server: https://192.168.108.160:6443

curl --cert ./client.pem --key ./client-key.pem --cacert ./ca.pem https://192.168.108.160:6443/api/v1/pods

curl --cert ~/client.pem --key ~/client-key.pem --cacert ~/ca.pem https://192.168.108.160:6443/api/v1/namespaces/default/pods -XPOST -H'Content-Type: application/json' -d@curlpod.json

kubectl get pods
```

# 5.2
```bash 
kubectl get endpoints

strace kubectl get endpoints

cd /home/student/.kube/cache/discovery/192.168.108.160_6443

find .

python -m json.tool v1/serverresources.json

python -m json.tool v1/serverresources.json | less

kubectl get ep

python -m json.tool v1/serverresources.json | grep kind

python -m json.tool apps/v1beta1/serverresources.json | grep kind

kubectl delete po curlpod
```

* kubectl api-versions
* kubectl api-resources
* kubectl explain {object}

# 6.1
```bash 
kubectl config view

kubectl get secrets --all-namespaces

kubectl get secrets

kubectl describe secrets default-token-6j2ws

export token=$(kubectl describe secrets default-token-6j2ws | grep ^token | cut -f7 -d ' ')

curl https://192.168.108.160:6443/apis --header "Authorization: bearer $token" -k

curl https://192.168.108.160:6443/api/v1 --header "Authorization: bearer $token" -k

curl https://192.168.108.160:6443/api/v1/namespaces --header "Authorization: bearer $token" -k

kubectl run -i -t busybox --image=busybox --restart=Never

ls /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```

# 6.2
```bash 
kubectl proxy -h

kubectl proxy --api-prefix=/

kubectl proxy --api-prefix=/ &

curl http://127.0.0.1:8001/api

curl http://127.0.0.1:8001/api/v1/namespaces
```

# 6.3
```bash 
kubectl create -f job.yaml

kubectl get job

kubectl describe jobs.batch sleepy

kubectl get jobs.batch sleepy -o yaml

kubectl delete jobs.batch sleepy

vi job.yaml
#spec:
#  completions: 5   <== add
#  template:
#    spec:

kubectl create -f job.yaml

kubectl get job

kubectl get pod

kubectl delete jobs.batch sleepy

vi job.yaml
#spec:
#  completions: 5
#  parallelism: 2   <== add
#  template:
#    spec:

kubectl create -f job.yaml

kubectl get pod

kubectl get job

vi job.yaml
#spec:
#  completions: 5
#  parallelism: 2
#  activeDeadlineSeconds: 15  <== add
#  template:
#    spec:
#      containers:
#      - name: resting
#        image: busybox
#        command: ["/bin/sleep"]
#        args: ["5"]  <== edit

kubectl delete jobs.batch sleepy

kubectl create -f job.yaml

kubectl get job

kubectl get job sleepy -o yaml

kubectl delete jobs.batch sleepy
```

# 6.4
```bash 
cp job.yaml cronjob.yaml

vi cronjob.yaml
#apiVersion: batch/v1beta1
#kind: CronJob
#metadata:
#  name: sleepy
#spec:
#  schedule: "*/2 * * * *"
#  jobTemplate:
#    spec:
#      template:
#        spec:
#          containers:
#          - name: resting
#            image: busybox
#            command: ["/bin/sleep"]
#            args: ["3"]
#          restartPolicy: Never

kubectl create -f cronjob.yaml

kubectl get cronjob

kubectl get job

vi cronjob.yaml
#apiVersion: batch/v1beta1
#kind: CronJob
#metadata:
#  name: sleepy
#spec:
#  schedule: "*/2 * * * *"
#  jobTemplate:
#    spec:
#      template:
#        spec:
$          activeDeadlineSeconds: 10  <== Add
#          containers:
#          - name: resting
#            image: busybox
#            command: ["/bin/sleep"]
#            args: ["30"]
#          restartPolicy: Never

kubectl delete cronjobs.batch sleepy

kubectl create -f cronjob.yaml

kubectl get cronjob

kubectl get job

kubectl delete cronjobs.batch sleepy
```
