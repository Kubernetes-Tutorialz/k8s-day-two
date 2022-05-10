# Services

## Retomando nossos testes com o services

- Vamos criar export nosso service com usando o *type* para `LoadBalancer`:

```bash
# kubectl expose deployment nginx --type=LoadBalancer --port=80
service/nginx exposed
```

- Vejamos:

```bash
# kubectl get svc nginx 
NAME    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
nginx   LoadBalancer   10.111.146.234   <pending>     80:31582/TCP   24s
```

- Vamos agora pegar esse novo modelo de *services* que criamos para melhorar ele:

```yml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2022-03-07T08:31:10Z"
  labels:
    app: nginx
  name: nginx
  namespace: default
  resourceVersion: "552070"
  uid: 6c12acc7-1e6e-4c12-93e8-1d46a7314381
spec:
  allocateLoadBalancerNodePorts: true
  clusterIP: 10.111.146.234
  clusterIPs:
  - 10.111.146.234
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 31582
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: LoadBalancer
status:
  loadBalancer: {}
```

- Melhorando nosso `YML` file:

```yml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  allocateLoadBalancerNodePorts: true
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 32582
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: LoadBalancer
```

- Agora vamos fazer o *create* desse *service*:

```bash
# kubectl create -f service_loadbalancer.yml
service/nginx created
```

-   Agora analisando o *services*:

```bash
# kubectl get svc nginx
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx   LoadBalancer   10.107.206.25   <pending>     80:32583/TCP   4s
```

-   Descrevendo nosso *endpoint*:

```bash
# kubectl describe  endpoints nginx
Name:         nginx  
Namespace:    default
Labels:       app=nginx
Annotations:  endpoints.kubernetes.io/last-change-trigger-time: 2022-03-07T09:30:13Z
Subsets:
  Addresses:          10.47.0.1
  NotReadyAddresses:  <none>
  Ports:
    Name     Port  Protocol
    ----     ----  --------
    <unset>  80    TCP

Events:  <none>
```

-   Verificado:

```bash
# kubectl get endpoints nginx 
NAME    ENDPOINTS      AGE     
nginx   10.47.0.1:80   2m2s
```

-   Analisando o *endpoint*:

```bash
# kubectl get endpoints
NAME         ENDPOINTS            AGE  
kubernetes   192.168.0.234:6443   7d17h
nginx        10.47.0.1:80         118s
```

