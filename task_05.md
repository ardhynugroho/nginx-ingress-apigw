# Task 05: Authorization - Signed JSON Web Token (JWT) Validation

Restrict access to authorized users only.

## Steps

1.

```
cd ../task_05
```

2.

```
create-signed-jwt.sh
```

Example JWT output: `Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJpc3N1ZXIiLCJzdWIiOiJzdWJqZWN0IiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE4OTM0NTYwMDAsIm5iZiI6MTY2NDcxMDAyMiwiaWF0IjoxNjY0NzEwMDIyLCJqdGkiOiJpZDEyMzQ1NiJ9Cg.LZ0OvYcZpwRCumDzijS5ZSXHno2gzL4O9VSSm-yyfmuQznk_bq609u-UGm3eaBF8kmnokVlMJ_2J0uAaNfmKOU_EPnoIRNH7NPimyofDlRFpztP-eVMTI5hlE0PLGV4YFsi_aOXo1iDJUKEQivxmWkJuLIU4THoktMM0HyrM1Ic3ioB2NpNZ9ZdWLhqdg74Q-Dd-jzwX6CQemftGwMTUGxUxSL1l25-V4JMbJDfHXYThvH3AoQQCIqbIf5yM-XtuN5sxTvKAhozJd-Rf2H_UpC5f2ip86WAvPCBTLAlqGqcl44DzsnQ7MFhZWwNocalLuRctHWE5_uFCRvrXWCaWbQ`

3.
```
kubectl create secret generic jwk-secret --from-file=jwk=/var/tmp/jwk/jwk.json --type=nginx.org/jwk
```

4.

```
kubectl apply -f jwt-policy.yaml
```

5.
```
kubectl apply -f VirtualServer.yaml
```

<details>
  <summary>Config changes</summary>

``` diff
#task_05$ diff VirtualServer.yaml ../task_04/VirtualServer.yaml
38,39d37
<       - name: jwt-policy
<         namespace: default
```

</details>

## Result

Add new job without Authorization token

```
curl -k  -X POST -H "Content-Type: application/json" -d '["Signed JWT Creator"]' https://jobs.local/add-job
```

Request rejected!

```
{
   "supportID": "1181064925857463760"
}
```

Retry with Authorization token

Use your authorization token generated at step-2

```
curl -k  -X POST -H "Content-Type: application/json" -d '["Signed JWT Creator"]' -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJpc3N1ZXIiLCJzdWIiOiJzdWJqZWN0IiwiYXVkIjoiYXVkaWVuY2UiLCJleHAiOjE4OTM0NTYwMDAsIm5iZiI6MTY2NDcxMDAyMiwiaWF0IjoxNjY0NzEwMDIyLCJqdGkiOiJpZDEyMzQ1NiJ9Cg.LZ0OvYcZpwRCumDzijS5ZSXHno2gzL4O9VSSm-yyfmuQznk_bq609u-UGm3eaBF8kmnokVlMJ_2J0uAaNfmKOU_EPnoIRNH7NPimyofDlRFpztP-eVMTI5hlE0PLGV4YFsi_aOXo1iDJUKEQivxmWkJuLIU4THoktMM0HyrM1Ic3ioB2NpNZ9ZdWLhqdg74Q-Dd-jzwX6CQemftGwMTUGxUxSL1l25-V4JMbJDfHXYThvH3AoQQCIqbIf5yM-XtuN5sxTvKAhozJd-Rf2H_UpC5f2ip86WAvPCBTLAlqGqcl44DzsnQ7MFhZWwNocalLuRctHWE5_uFCRvrXWCaWbQ" https://jobs.local/add-job
```

Request allowed!

```
[
  "Mortician","Civil Rights Activist",

  <<< cut >>>

  "Professional Skateboarder",
  42,
  true,
  "OpenAPI Spec Enforcer",
  "Signed JWT Creator"   <<<===
]
```
