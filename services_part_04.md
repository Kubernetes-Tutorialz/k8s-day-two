## Services

### Retomando nossos testes com o services

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