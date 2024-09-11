# Observability Demo: NGINX One

1. Dockerfile

```
FROM localhost:32000/nginx-plus-ingress-nap:3.3.2
USER root
RUN apt -y update
RUN apt -y install curl
RUN curl https://agent.connect.nginx.com/nginx-agent/install | DATA_PLANE_KEY="7LqD1O7eQVOAGfDnls5/Px3riuWCY8MmUced1MQffSM=" sh -s -- -y ; exit 0
```

2. Build image
```
docker build -t tes .
```

3. List new image
```
$ docker image list
REPOSITORY                               TAG       IMAGE ID       CREATED         SIZE
tes                                      latest    83b9f6752312   2 hours ago     909MB
<none>                                   <none>    a659404f7f0e   3 hours ago     859MB
localhost:32000/nginx-plus-ingress-nap   3.3.2     49603e583e71   10 months ago   859MB
localhost:32000/nginx-plus-ingress-dos   3.3.2     f43873f32f47   10 months ago   266MB
localhost:32000/nginx-plus-ingress       3.3.2     7af60ee6a57f   10 months ago   257MB
```

4. Tag the image
```
docker image tag tes localhost:32000/tes
```

```
$ docker image list
REPOSITORY                               TAG       IMAGE ID       CREATED         SIZE
tes                                      latest    83b9f6752312   2 hours ago     909MB
localhost:32000/tes                      latest    83b9f6752312   2 hours ago     909MB
<none>                                   <none>    a659404f7f0e   3 hours ago     859MB
localhost:32000/nginx-plus-ingress-nap   3.3.2     49603e583e71   10 months ago   859MB
localhost:32000/nginx-plus-ingress-dos   3.3.2     f43873f32f47   10 months ago   266MB
localhost:32000/nginx-plus-ingress       3.3.2     7af60ee6a57f   10 months ago   257MB

```

5. Push to local repo
```
docker push localhost:32000/tes
```

6. Get nginx-ingress deployment
```
kubectl get daemonset -n nginx-ingress -o yaml > nginx-ingress.yaml
```

``` yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    annotations:
      deprecated.daemonset.template.generation: "2"
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"apps/v1","kind":"DaemonSet","metadata":{"annotations":{"deprecated.daemonset.template.generation":"1"},"creationTimestamp":"2024-04-10T15:54:21Z","generation":1,"name":"nginx-ingress","namespace":"nginx-ingress","resourceVersion":"529633","uid":"d2687812-7a86-47ac-b287-2c02eab2aee3"},"spec":{"revisionHistoryLimit":10,"selector":{"matchLabels":{"app":"nginx-ingress"}},"template":{"metadata":{"creationTimestamp":null,"labels":{"app":"nginx-ingress","app.kubernetes.io/name":"nginx-ingress"}},"spec":{"automountServiceAccountToken":true,"containers":[{"args":["-nginx-plus","-nginx-configmaps=$(POD_NAMESPACE)/nginx-config","-enable-app-protect"],"env":[{"name":"POD_NAMESPACE","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"metadata.namespace"}}},{"name":"POD_NAME","valueFrom":{"fieldRef":{"apiVersion":"v1","fieldPath":"metadata.name"}}}],"image":"localhost:32000/tes","imagePullPolicy":"IfNotPresent","name":"nginx-plus-ingress","ports":[{"containerPort":80,"hostPort":80,"name":"http","protocol":"TCP"},{"containerPort":443,"hostPort":443,"name":"https","protocol":"TCP"},{"containerPort":8081,"name":"readiness-port","protocol":"TCP"},{"containerPort":9113,"name":"prometheus","protocol":"TCP"}],"readinessProbe":{"failureThreshold":3,"httpGet":{"path":"/nginx-ready","port":"readiness-port","scheme":"HTTP"},"periodSeconds":1,"successThreshold":1,"timeoutSeconds":1},"resources":{"requests":{"cpu":"100m","memory":"128Mi"}},"securityContext":{"allowPrivilegeEscalation":false,"capabilities":{"add":["NET_BIND_SERVICE"],"drop":["ALL"]},"runAsNonRoot":true,"runAsUser":101},"terminationMessagePath":"/dev/termination-log","terminationMessagePolicy":"File"}],"dnsPolicy":"ClusterFirst","restartPolicy":"Always","schedulerName":"default-scheduler","securityContext":{"seccompProfile":{"type":"RuntimeDefault"}},"serviceAccount":"nginx-ingress","serviceAccountName":"nginx-ingress","terminationGracePeriodSeconds":30}},"updateStrategy":{"rollingUpdate":{"maxSurge":0,"maxUnavailable":1},"type":"RollingUpdate"}},"status":{"currentNumberScheduled":1,"desiredNumberScheduled":1,"numberAvailable":1,"numberMisscheduled":0,"numberReady":1,"observedGeneration":1,"updatedNumberScheduled":1}}
    creationTimestamp: "2024-04-10T15:54:21Z"
    generation: 2
    name: nginx-ingress
    namespace: nginx-ingress
    resourceVersion: "674917"
    uid: d2687812-7a86-47ac-b287-2c02eab2aee3
  spec:
    revisionHistoryLimit: 10
    selector:
      matchLabels:
        app: nginx-ingress
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: nginx-ingress
          app.kubernetes.io/name: nginx-ingress
      spec:
        automountServiceAccountToken: true
        containers:
        - args:
          - -nginx-plus
          - -nginx-configmaps=$(POD_NAMESPACE)/nginx-config
          - -enable-app-protect
          env:
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
          - name: POD_NAME
            valueFrom:
              fieldRef:
                apiVersion: v1
                fieldPath: metadata.name
          image: localhost:32000/nginx-plus-ingress-nap:3.3.2   <<<===
          imagePullPolicy: IfNotPresent
          name: nginx-plus-ingress
          ports:
          - containerPort: 80
            hostPort: 80
            name: http
            protocol: TCP
          - containerPort: 443
            hostPort: 443
            name: https
            protocol: TCP
          - containerPort: 8081
            name: readiness-port
            protocol: TCP
          - containerPort: 9113
            name: prometheus
            protocol: TCP
          readinessProbe:
            failureThreshold: 3
            httpGet:
              path: /nginx-ready
              port: readiness-port
              scheme: HTTP
            periodSeconds: 1
            successThreshold: 1
            timeoutSeconds: 1
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
          securityContext:
            allowPrivilegeEscalation: false
            capabilities:
              add:
              - NET_BIND_SERVICE
              drop:
              - ALL
            runAsNonRoot: true
            runAsUser: 101
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext:
          seccompProfile:
            type: RuntimeDefault
        serviceAccount: nginx-ingress
        serviceAccountName: nginx-ingress
        terminationGracePeriodSeconds: 30
    updateStrategy:
      rollingUpdate:
        maxSurge: 0
        maxUnavailable: 1
      type: RollingUpdate
  status:
    currentNumberScheduled: 1
    desiredNumberScheduled: 1
    numberAvailable: 1
    numberMisscheduled: 0
    numberReady: 1
    observedGeneration: 2
    updatedNumberScheduled: 1
kind: List
metadata:
  resourceVersion: ""
```

7. update `nginx-ingress.yaml` file, change `image: localhost:32000/nginx-plus-ingress-nap:3.3.2`  to `image: localhost:32000/tes`
8. apply new config `kubectl apply -f nginx-ingress.yaml`
9. check updated config
```
ame:             nginx-ingress-8wm8n
Namespace:        nginx-ingress
Priority:         0
Service Account:  nginx-ingress
Node:             microk8s-1/10.1.1.4
Start Time:       Wed, 11 Sep 2024 04:52:11 +0000
Labels:           app=nginx-ingress
                  app.kubernetes.io/name=nginx-ingress
                  app.kubernetes.io/version=3.3.2
                  app.nginx.org/version=1.25.1-nginx-plus-r30-p1
                  controller-revision-hash=bc9c478cd
                  pod-template-generation=2
Annotations:      cni.projectcalico.org/containerID: ea997af1839692219adfc6eec079be8ff70df3f9b84e928d6cc0299e808e743c
                  cni.projectcalico.org/podIP: 10.1.35.157/32
                  cni.projectcalico.org/podIPs: 10.1.35.157/32
Status:           Running
SeccompProfile:   RuntimeDefault
IP:               10.1.35.157
IPs:
  IP:           10.1.35.157
Controlled By:  DaemonSet/nginx-ingress
Containers:
  nginx-plus-ingress:
    Container ID:  containerd://abe8c9ceaf098231ea8887f3f3a20ab955eb6c7df2aa849f1875562ee391aad3
    Image:         localhost:32000/tes    <<<===
    Image ID:      localhost:32000/tes@sha256:0e54e6c2598203d192ee36b45f965444f0b65abc25d5e42386e4a3fef0567793
    Ports:         80/TCP, 443/TCP, 8081/TCP, 9113/TCP
    Host Ports:    80/TCP, 443/TCP, 0/TCP, 0/TCP
    Args:
      -nginx-plus
      -nginx-configmaps=$(POD_NAMESPACE)/nginx-config
      -enable-app-protect
    State:          Running
      Started:      Wed, 11 Sep 2024 04:52:13 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:      100m
      memory:   128Mi
    Readiness:  http-get http://:readiness-port/nginx-ready delay=0s timeout=1s period=1s #success=1 #failure=3
    Environment:
      POD_NAMESPACE:  nginx-ingress (v1:metadata.namespace)
      POD_NAME:       nginx-ingress-8wm8n (v1:metadata.name)
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-s99c8 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-s99c8:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/disk-pressure:NoSchedule op=Exists
                             node.kubernetes.io/memory-pressure:NoSchedule op=Exists
                             node.kubernetes.io/not-ready:NoExecute op=Exists
                             node.kubernetes.io/pid-pressure:NoSchedule op=Exists
                             node.kubernetes.io/unreachable:NoExecute op=Exists
                             node.kubernetes.io/unschedulable:NoSchedule op=Exists
Events:                      <none>
```
