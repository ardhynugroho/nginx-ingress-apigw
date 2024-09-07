# Task 04: OpenAPI Spec Enforcement with NGINX App Protect

Test add new job

```
curl -k -X POST -H "Content-Type: application/json"  -d '["Professional Skateboarder"]' https://jobs.local/add-job | jq
```

You can see new job "Professional Skateboarder" was added

Now put some bad job information

```
curl -k -X POST -H "Content-Type: application/json"  -d '[42, true]' https://jobs.local/add-job | jq
```

You can see bad job name was added.

We need OpenAPI validation to protect the API from bad payload.

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

Create request with bad payload

```
$ curl -k -X POST -H "Content-Type: application/json"  -d '[42, true]' https://jobs.local/add-job | jq
```

Request is rejected with support ID

```
{
  "supportID": "4428710366475055126"
}
```

Now create request with good payload

```
curl -k -X POST -H "Content-Type: application/json"  -d '["OpenAPI Spec Enforcer"]' https://jobs.local/add-job | jq
```

You can see new job was added
