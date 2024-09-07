# Copy-Paste Guide: NGINX ingress controller as API Gateway

## Task 00
Do these inside microk8s-1 node

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
you might terminate with c-c

6.
```
curl http://localhost:32000/v2/_catalog | jq
```
7.
```
list-all-k8s-lab-resources.sh
```
Give --start-over parameter to cleanup the lab
