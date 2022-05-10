# Services

### Create vs Apply

1.  *Create*: usado dentro da abordagem de `Imperative Management`.
    1.  você informa à API do Kubernetes o que deseja criar, substituir ou excluir.
   
2.  *Apply*: usado dentro da abordagem de `Declarative Management`.
    1.  abordagem onde as alterações que você pode ter aplicado a um objeto ao vivo (ou seja, através de escala) são "mantidas" mesmo se você aplicar outras alterações ao objeto.

3.  Vamos aos exemplos abaixo:

-   Criando um deployment com a imagem do `NGNX`:

```bash
# kubectl create deployment nginx --image=nginx
deployment.apps/nginx create
```

-   Descrevendo nosso `deployment`:

```bash
# kubectl get deployments.apps 
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           7s
```

-   Expondo a a porta do nosso `service`:

```bash
# kubectl expose  deployment nginx --port=80
service/nginx exposed
```

- Analisando nosso *service* do `NGINX`:

```bash
# kubectl get svc nginx 
NAME    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginx   ClusterIP   10.96.216.79   <none>        80/TCP    2m52s
```

- Vamos agora testar usando o comando `curl` se ele esta acesando a pagina do `NGINX`:

```html
# curl 10.96.216.79
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

-   Analisando nosso *deployment* do `NGINX`:

```bash
# kubectl get deployments.apps nginx 
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m30s
```

-   Descrevendo nosso *service*:

```bash
# kubectl describe svc nginx 
Name:              nginx
Namespace:         default
Labels:            app=nginx
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.96.216.79
IPs:               10.96.216.79
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         10.47.0.1:80
Session Affinity:  None
Events:            <none>
```

-   Vamos criar um *service* padrao:

```yml
# kubectl get svc nginx -o yaml 
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2022-03-07T07:49:28Z"
  labels:
    app: nginx
  name: nginx
  namespace: default
  resourceVersion: "548606"
  uid: 2396edbb-08c6-4e57-a587-c5ef58a387f2
spec:
  clusterIP: 10.96.216.79
  clusterIPs:
  - 10.96.216.79
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```

-   Agora vamos melhorar esse *service* para que possamos usar ele:
-   Campos que eu tirei:
    -   creationTimestamp: "2022-03-07T07:49:28Z"
    -   resourceVersion: "548606"
    -   status: loadBalancer: {}
    -   uid: 2396edbb-08c6-4e57-a587-c5ef58a387f2

```yml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  clusterIP: 10.96.216.79
  clusterIPs:
  - 10.96.216.79
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - nodePort: 32100
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  sessionAffinity: None
  type: NodePort
```

-   Feito isso vamos criar nosso *service*:

```bash
# kubectl create -f service_nodeport.yml 
service/nginx created
```

- Veja abaixo nosso *service* do `NGINX` com *type* `NodePort` e agora exposto na porta `80:32100/TCP`:
- No browser voce pode por o IP do seu cluster apostando para essa porta.

```bash
# kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE  
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        7d16h
nginx        NodePort    10.96.216.79   <none>        80:32100/TCP   9s
```
