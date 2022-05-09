## Services


1. Iniciando a aula e vendo que nosso cluster esta funcionando legal:

```bash
# kubectl get nodes
NAME                    STATUS   ROLES                  AGE    VERSION
kube-worker01           Ready    <none>                 9h     v1.23.1
kube-worker2            Ready    <none>                 114s   v1.23.1
localhost.localdomain   Ready    control-plane,master   10h    v1.23.1
```
1.  A)Vamos ver se temos o *deployment*:

```bash
# kubectl get deployments
No resources found in default namespace.
```

    B) Agora analisando se temos *services*:

```bash
# kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE 
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7d9h
```

