# Task 04: OpenAPI Spec Enforcement with NGINX App Protect

Test add new job

```
curl -k -X POST -H "Content-Type: application/json"  -d '["Professional Skateboarder"]' https://jobs.local/add-job | jq
```

You can see new job "Preofessional Skateboarder" was added

## Steps

1.

```
cd ../task_04
```

2.
**IMPORTANT**

OpenAPI spec. file URL is updated to `https://raw.githubusercontent.com/tmarfil/nginx-api-gateway-for-k8s/main/task_04/jobs-openapi-spec.yaml`

Use following command to fix the URL

```
sed -i 's/appworld-2024-//g' jobs-openapi-spec-appolicy.yaml
```

3.
```
k apply -f jobs-openapi-spec-appolicy.yaml
```

4.
```
k apply -f app-protect-policy.yaml
```

<details>
  <summary>File content</summary>

``` yaml
apiVersion: k8s.nginx.org/v1
kind: Policy
metadata:
  name: app-protect-policy
spec:
  waf:
    enable: true
    apPolicy: "default/jobs-openapi-spec"
```

</details>

5.
```
k apply -f VirtualServer.yaml
```

<details>
  <summary>Added policy configuration to /get-job and /add-job endpoint</summary>

``` diff
29,31d28
<       policies:
<       - name: app-protect-policy
<         namespace: default
37,39d33
<       policies:
<       - name: app-protect-policy
<         namespace: default
```

</details>

## Result

Payload not conform with OpenAPI spec will be rejected with support ID

```
$ curl -k -X POST -H "Content-Type: application/json"  -d '[42, true]' https://jobs.local/add-job | jq
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    47  100    37  100    10   1473    398 --:--:-- --:--:-- --:--:--  1958
{
  "supportID": "4428710366475055126"
}
```

Payload that conform the spec. is allowed

```
curl -k -X POST -H "Content-Type: application/json"  -d '["OpenAPI Spec Enforcer"]' https://jobs.local/add-job | jq
```
