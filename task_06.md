# Task 06: API Request Rate-Limiting



## Steps @ jumphost !!!

1. Download k6 test file
   
```
wget https://raw.githubusercontent.com/tmarfil/nginx-api-gateway-for-k8s/main/jumphost/k6-jobs.js
```

2. Edit `k6-jobs.js` file to update the Authorization token

`sed -i 's/"Authorization": "Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJpc3N1ZXIiLCJzdWIiOiJzdWJqZWN0IiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE4OTM0NTYwMDAsIm5iZiI6MTY2NDcxMDAyMiwiaWF0IjoxNjY0NzEwMDIyLCJqdGkiOiJpZDEyMzQ1NiJ9Cg.X0PnHJ7YWWqc4rBTbABII72vfm0wOjrSVJqBgO9MQVxrU7g19NvH0ZWAPTXwEHyW3C1cTkXtEgjK5in96JT_9jFwVHxV1N5fGDTL5g0GElza6vWi82Cp2xxyj7LOMZGfZALjrKMKzTPStrC2dMV1P6L-mWsOZ7p9FyDaR5Cnrd5OOLb_S-zFa7hAO9HalAVsfzlmMVju5uliQLBiRBmSUa1AxH57U-7Zjrln8HwO3SyQbYRJYqj-gCHwqc7NuOFlf3w6Ryldvr6Q2HWaZ6vZ2yyKvG8OPXUXPDsb1v6zoHUqyiMKBfwlZ7JDuhD7e_p5k3ft0dmSZaEhJYo87PkwnQ"/`__REPLACE_WITH_YOUR_AUTHZ_TOKEN_IN_JSON_FORMAT__`/g' k6-jobs.js`

Example:

`sed -i 's/"Authorization": "Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJpc3N1ZXIiLCJzdWIiOiJzdWJqZWN0IiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE4OTM0NTYwMDAsIm5iZiI6MTY2NDcxMDAyMiwiaWF0IjoxNjY0NzEwMDIyLCJqdGkiOiJpZDEyMzQ1NiJ9Cg.X0PnHJ7YWWqc4rBTbABII72vfm0wOjrSVJqBgO9MQVxrU7g19NvH0ZWAPTXwEHyW3C1cTkXtEgjK5in96JT_9jFwVHxV1N5fGDTL5g0GElza6vWi82Cp2xxyj7LOMZGfZALjrKMKzTPStrC2dMV1P6L-mWsOZ7p9FyDaR5Cnrd5OOLb_S-zFa7hAO9HalAVsfzlmMVju5uliQLBiRBmSUa1AxH57U-7Zjrln8HwO3SyQbYRJYqj-gCHwqc7NuOFlf3w6Ryldvr6Q2HWaZ6vZ2yyKvG8OPXUXPDsb1v6zoHUqyiMKBfwlZ7JDuhD7e_p5k3ft0dmSZaEhJYo87PkwnQ"/`**"Authorization": "Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJpc3N1ZXIiLCJzdWIiOiJzdWJqZWN0IiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE4OTM0NTYwMDAsIm5iZiI6MTY2NDcxMDAyMiwiaWF0IjoxNjY0NzEwMDIyLCJqdGkiOiJpZDEyMzQ1NiJ9Cg.LZ0OvYcZpwRCumDzijS5ZSXHno2gzL4O9VSSm-yyfmuQznk_bq609u-UGm3eaBF8kmnokVlMJ_2J0uAaNfmKOU_EPnoIRNH7NPimyofDlRFpztP-eVMTI5hlE0PLGV4YFsi_aOXo1iDJUKEQivxmWkJuLIU4THoktMM0HyrM1Ic3ioB2NpNZ9ZdWLhqdg74Q-Dd-jzwX6CQemftGwMTUGxUxSL1l25-V4JMbJDfHXYThvH3AoQQCIqbIf5yM-XtuN5sxTvKAhozJd-Rf2H_UpC5f2ip86WAvPCBTLAlqGqcl44DzsnQ7MFhZWwNocalLuRctHWE5_uFCRvrXWCaWbQ"**`/g' k6-jobs.js`

3.
```
k6 run k6-jobs.js --insecure-skip-tls-verify
```

Run result:

```

          /\      |‾‾| /‾‾/   /‾‾/
     /\  /  \     |  |/  /   /  /
    /  \/    \    |     (   /   ‾‾\
   /          \   |  |\  \ |  (‾)  |
  / __________ \  |__| \__\ \_____/ .io

  execution: local
     script: k6-jobs.js
     output: -

  scenarios: (100.00%) 1 scenario, 10 max VUs, 1m0s max duration (incl. graceful stop):
           * default: 10 looping VUs for 30s (gracefulStop: 30s)


     ✓ is status 200

     checks.........................: 100.00% ✓ 24353      ✗ 0
     data_received..................: 6.4 MB  212 kB/s
     data_sent......................: 16 MB   544 kB/s
     http_req_blocked...............: avg=53.85µs min=1.15µs med=2.87µs  max=16.9ms  p(90)=4.7µs    p(95)=5.63µs
     http_req_connecting............: avg=8.37µs  min=0s     med=0s      max=5.2ms   p(90)=0s       p(95)=0s
     http_req_duration..............: avg=12.1ms  min=2.49ms med=10.99ms max=89.03ms p(90)=17.16ms  p(95)=20.95ms
       { expected_response:true }...: avg=12.1ms  min=2.49ms med=10.99ms max=89.03ms p(90)=17.16ms  p(95)=20.95ms
     http_req_failed................: 0.00%   ✓ 0          ✗ 24353
     http_req_receiving.............: avg=67.29µs min=17.2µs med=54.89µs max=7.08ms  p(90)=106.82µs p(95)=130.72µs
     http_req_sending...............: avg=20.8µs  min=7.06µs med=13.37µs max=4.95ms  p(90)=38.34µs  p(95)=53.79µs
     http_req_tls_handshaking.......: avg=40.62µs min=0s     med=0s      max=16.2ms  p(90)=0s       p(95)=0s
     http_req_waiting...............: avg=12.01ms min=2.44ms med=10.91ms max=88.96ms p(90)=17.04ms  p(95)=20.85ms
 >>> http_reqs......................: 24353   811.589748/s   <<<
     iteration_duration.............: avg=12.3ms  min=2.66ms med=11.16ms max=89.34ms p(90)=17.45ms  p(95)=21.2ms
     iterations.....................: 24353   811.589748/s
     vus............................: 10      min=10       max=10
     vus_max........................: 10      min=10       max=10


running (0m30.0s), 00/10 VUs, 24353 complete and 0 interrupted iterations
default ✓ [======================================] 10 VUs  30s
```

You can see request rate is: `http_reqs......................: 24353   811.589748/s`

## Steps @ microk8s-1 !!!

4.
```
cd ../task_06
```

5.
```
k apply -f rate-limit-policy.yaml
```

6. Verify that rate limit policy configured at 10 rps

```
k describe policy rate-limit-policy
```

```
Name:         rate-limit-policy
Namespace:    default

<<< CUT >>>

Spec:
  Rate Limit:
    Key:          ${http_authorization}
    Rate:         10r/s      <<<===
    Reject Code:  429
    Zone Size:    10M

<<< CUT >>>
```
7.
```
k apply -f VirtualServer.yaml
```

<details>
  <summary>Config changes</summary>

``` diff
#task_06$ diff VirtualServer.yaml ../task_05/VirtualServer.yaml
30c30
<       - name: rate-limit-policy
---
>       - name: app-protect-policy
```

</details>

## Steps @ jumphost !!!
8.
```
k6 run k6-jobs.js --insecure-skip-tls-verify
```
Run result:

```

          /\      |‾‾| /‾‾/   /‾‾/
     /\  /  \     |  |/  /   /  /
    /  \/    \    |     (   /   ‾‾\
   /          \   |  |\  \ |  (‾)  |
  / __________ \  |__| \__\ \_____/ .io

  execution: local
     script: k6-jobs.js
     output: -

  scenarios: (100.00%) 1 scenario, 10 max VUs, 1m0s max duration (incl. graceful stop):
           * default: 10 looping VUs for 30s (gracefulStop: 30s)


     ✗ is status 200
      ↳  23% — ✓ 59 / ✗ 190

     checks.........................: 23.69% ✓ 59       ✗ 190
     data_received..................: 105 kB 3.4 kB/s
     data_sent......................: 170 kB 5.4 kB/s
     http_req_blocked...............: avg=669.45µs min=1.67µs   med=4.98µs  max=26.21ms  p(90)=9.57µs   p(95)=24.78µs
     http_req_connecting............: avg=184.89µs min=0s       med=0s      max=8.01ms   p(90)=0s       p(95)=0s
     http_req_duration..............: avg=2.33ms   min=672.39µs med=1.54ms  max=15.23ms  p(90)=3.68ms   p(95)=7.02ms
       { expected_response:true }...: avg=3.43ms   min=2.28ms   med=2.96ms  max=13.28ms  p(90)=4.61ms   p(95)=5.98ms
     http_req_failed................: 76.30% ✓ 190      ✗ 59
     http_req_receiving.............: avg=89.34µs  min=13.64µs  med=69.15µs max=695.45µs p(90)=164.83µs p(95)=216.9µs
     http_req_sending...............: avg=34.31µs  min=7.09µs   med=25.12µs max=428.73µs p(90)=58.03µs  p(95)=74.01µs
     http_req_tls_handshaking.......: avg=473.9µs  min=0s       med=0s      max=18.07ms  p(90)=0s       p(95)=0s
     http_req_waiting...............: avg=2.21ms   min=586.2µs  med=1.43ms  max=14.95ms  p(90)=3.59ms   p(95)=6.89ms
 >>> http_reqs......................: 249    8.002752/s   <<<===
     iteration_duration.............: avg=1.24s    min=17.8ms   med=1.5s    max=1.51s    p(90)=1.5s     p(95)=1.5s
     iterations.....................: 249    8.002752/s
     vus............................: 9      min=9      max=10
     vus_max........................: 10     min=10     max=10


running (0m31.1s), 00/10 VUs, 249 complete and 0 interrupted iterations
default ✓ [======================================] 10 VUs  30s
```

You can see request rate was limited: `http_reqs......................: 249    8.002752/s`
