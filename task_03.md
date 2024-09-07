# Task 03: Complete the https://jobs.local application

|Outside K8s Cluster	| Inside K8s Cluster
|--|---|
|https://jobs.local/get-job	|http://eclectic-jobs:3000/|
|https://jobs.local/add-job	|http://eclectic-jobs:3000/add-job|
|https://jobs.local/	|http://myapp:3000|

## Steps

1.
```
cd ../task_03
```

2.
```
k apply -f VirtualServer.yaml
```

<details>
  <summary>Examine the new config</summary>

``` diff
diff task_03/VirtualServer.yaml  task_02/VirtualServer.yaml
15,17d14
<     - name: myapp-upstream
<       service: myapp
<       port: 3000
22,27d18
<     - path: /
<       action:
<         pass: myapp-upstream
<     - path: /index.html
<       action:
<         pass: myapp-upstream
```

</details>

## Result

<img width="743" alt="image" src="https://github.com/user-attachments/assets/8ab424c0-f287-43c9-8c59-e7d715fcc4b6">
