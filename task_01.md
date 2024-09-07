# Task 01: Create API Service in Kubernetes

|NodePort Service|Inside K8s Cluster|
|---|---|
|http://jobs.local:30020|http://eclectic-job:3000|
|http://jobs.local:30010|http://myapp:3000|

## Steps

1.
```
cd task_01
```
The full path sould be `/home/ubuntu/nginx-api-gateway-for-k8s/task_01`

2.
```
k apply -f jobs.yaml
```

<details>
<summary>File jobs.yaml</summary>
  
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: eclectic-jobs
  name: eclectic-jobs
spec:
  replicas: 1
  selector:
    matchLabels:
      app: eclectic-jobs
  template:
    metadata:
      labels:
        app: eclectic-jobs
    spec:
      containers:
      - image: tonymarfil/eclectic-jobs:v5
        imagePullPolicy: Always
        name: eclectic-jobs
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: eclectic-jobs
  name: eclectic-jobs
spec:
  ports:
  - name: 3000-3000
    nodePort: 30020
    port: 3000
    protocol: TCP
    targetPort: 3000
  selector:
    app: eclectic-jobs
  type: NodePort
```

</details>

3.
```
k apply -f main.yaml
```

<details>
  <summary>File main.yaml</summary>

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: myapp
  name: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - image: tonymarfil/myapp:v5
        imagePullPolicy: Always
        name: myapp
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: myapp
  name: myapp
spec:
  ports:
  - name: 3000-3000
    nodePort: 30010
    port: 3000
    protocol: TCP
    targetPort: 3000
  selector:
    app: myapp
  type: NodePort
status:
  loadBalancer: {}
```
</details>

## Results

<img width="743" alt="image" src="https://github.com/user-attachments/assets/67c0f466-8f12-47cb-b901-9282780d2aa9">

<img width="743" alt="image" src="https://github.com/user-attachments/assets/7f50c778-6b5b-4e8f-a282-f9138442fbcf">

