# Limites de Recursos

## Retomando nossos testes com limites de recursos

1.  Temos a opcao de editar um servico de forma "quente" diretamente na linha de comando:

```bash
# kubectl edit service nginx
service/nginx edited
```

2.  Podemos tambem editar o objeto do *deployment*:

```bash
# kubectl edit deployments.apps nginx
deployment.apps/nginx edited
```

3.  Analisando meus *POD's*:

```bash
# kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE  
nginx                    1/1     Running   0          25h  
nginx-85b98978db-ktkxq   1/1     Running   0          25h  
nginx-85b98978db-pzhlf   1/1     Running   0          3m30s
nginx-85b98978db-srpr6   1/1     Running   0          3m30s
nginx-85b98978db-w49lb   1/1     Running   0          3m30s
nginx-85b98978db-wt4kg   1/1     Running   0          3m30s
```

4.  Acessando meu container com `exec`:

```bash
# kubectl exec -ti nginx-85b98978db-ktkxq -- bash
root@nginx-85b98978db-ktkxq:/#
```

