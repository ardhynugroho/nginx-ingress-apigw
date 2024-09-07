# Task 02: Create https://jobs.local/get-job API endpoint

|Outside K8s Cluster|Inside K8s Cluster|
|---|---|
|https://jobs.local/get-job|http://eclectic-jobs:3000/|
|https://jobs.local/add-job|http://eclectic-jobs:3000/add-job|


## Steps

1.
```
cd ../task_02
```
The working directory should be: `/home/ubuntu/nginx-api-gateway-for-k8s/task_02`

2. Create SSL certificate to enable HTTPS
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout jobs.local.key -out jobs.local.crt -config openssl.cnf -extensions req_ext
```

3.
```
kubectl create secret tls jobs-local-tls --key jobs.local.key --cert jobs.local.crt
```

4.
```
kubectl get secret jobs-local-tls -o yaml
```

5.
```
k apply -f VirtualServer.yaml
```

<details>
  <summary>File VirtualServer.yaml</summary>

``` yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: my-virtualserver
  namespace: default
spec:
  host: jobs.local
  tls:
    secret: jobs-local-tls # replace with your actual secret name
    redirect:
      enable: true       # Enable automatic HTTP to HTTPS redirection
      code: 301          # Redirect with HTTP status code 301 (Moved Permanently)
      basedOn: scheme    # Redirect based on the scheme of the request
  upstreams:
    - name: eclectic-jobs-upstream
      service: eclectic-jobs
      port: 3000
  routes:
    - path: /get-job
      action:
        proxy:
          upstream: eclectic-jobs-upstream
          rewritePath: /
    - path: /add-job
      action:
        pass: eclectic-jobs-upstream
```

</details>

## Result

<img width="743" alt="image" src="https://github.com/user-attachments/assets/73ca8a68-b067-4438-b9ad-b4a6048f8b46">
<img width="743" alt="image" src="https://github.com/user-attachments/assets/a9f59d22-2452-491a-b1b5-4cc98e80f231">

