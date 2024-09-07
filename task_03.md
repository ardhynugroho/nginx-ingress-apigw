# Task 03: Task 03: Complete the https://jobs.local application

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
