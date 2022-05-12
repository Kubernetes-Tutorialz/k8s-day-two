## Namespaces no Kubernetes

Vamos falar aqui brevemente sobre *namespaces* dentro do Kubernetes, como ele funciona e como podemos aplicar. O Kubernetes possui esse recurso de *namespaces* onde voce acaba criando um projeto especifico, como se fosse um cercado mesmo para as aplicacoes que voce tem  no cluster.

Quando criamos um um *POD* se a gente nao especifica um *namespace* ele cria os objetos dentro do *namespace* default. Podemos ter varios ambinetes de uma aplicacao (PROD, DEV, TEST) e separar isso dentro do mesmo cluster, pois o *namespace* consegue criar essa separacao e manter tudo rodando de forma isolada, seja com permissoes para cada usuario e os ambientes.

1.  Vamos verificar como estao setados os *namespaces*:

```bash
# kubectl get namespaces 
NAME              STATUS   AGE
default           Active   8d 
kube-node-lease   Active   8d 
kube-public       Active   8d
kube-system       Active   8d
monitoring        Active   14h
```

2.  Criando um novo *namespace*

```bash
# kubectl create namespace devops
namespace/devops created
```

3.  Analisando novamnente os *namespaces*:

```bash
# kubectl get namespaces
NAME              STATUS   AGE
default           Active   8d
devops            Active   21s
kube-node-lease   Active   8d
kube-public       Active   8d
kube-system       Active   8d
monitoring        Active   14h
```

4.  Verificanos os detalhes do *namespace*:

```bash
# kubectl describe namespaces devops 
Name:         devops
Labels:       kubernetes.io/metadata.name=devops
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.
```

5.  Analisando o arquivo `YML` desse *namespace*:

`# kubectl get namespaces devops -o yaml`

```yml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2022-03-08T09:55:51Z"
  labels:
    kubernetes.io/metadata.name: devops    
  name: devops
  resourceVersion: "678211"
  uid: 362bb7f9-e94e-423c-a4d6-55f1a922b56a
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
```

