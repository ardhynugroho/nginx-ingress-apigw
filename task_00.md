# Task 00: Preparing The Lab Environment

## Steps @ microk8s-1 !!!

1.
```
git clone https://github.com/f5devcentral/nginx-api-gateway-for-k8s.git
```

2.
```
cd nginx-api-gateway-for-k8s
```

3.
```
cp -r ./bin/* ~/.local/bin/
```

4.
```
microk8s-status.sh
```

5.
```
test-dns.sh
```
you might terminate with Ctl-c

6.
```
curl http://localhost:32000/v2/_catalog | jq
```

7.
```
list-all-k8s-lab-resources.sh
```
Give `--start-over` parameter to cleanup the lab
