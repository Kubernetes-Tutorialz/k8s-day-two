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

6.  Melhorando nosso objeto de *namespace*:

```yml
piVersion: v1
kind: Namespace
metadata:
  labels:
    kubernetes.io/metadata.name: devops
  name: devops
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
```

7.  Agora vou aplicar esse *namespace* no meu cluster:

```bash
# kubectl apply -f namespace-test.yml 
namespace/girus created
```

8.  Veja como ficou o *namespace* `girus`:

```bash
# kubectl get namespaces 
NAME              STATUS   AGE
default           Active   8d
devops            Active   16m
girus             Active   8s
kube-node-lease   Active   8d
kube-public       Active   8d
kube-system       Active   8d
monitoring        Active   14h
```

9.  Agora vou listar os *namespaces* quu eu tenho aqui criado para o Grafana:

`# kubectl get all -n monitoring`

```bash
NAME                           READY   STATUS    RESTARTS   AGE
pod/grafana-5c447577c9-rcp8h   1/1     Running   0          5h56m

NAME              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/grafana   NodePort   10.109.75.97   <none>        3000:30431/TCP   14h

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           14h

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-5c447577c9   1         1         1       14h
```

`# kubectl get all -n monitoring-prod`

```bash
NAME                           READY   STATUS    RESTARTS   AGE
pod/grafana-5c447577c9-pcpts   1/1     Running   0          6m41s

NAME              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/grafana   NodePort   10.104.31.49   <none>        3000:30059/TCP   6m4s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           6m41s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-5c447577c9   1         1         1       6m41s
```

### Trabalhando com contexto no Kubernetes 

1.  Primeiro de tudo preciso ver como estao as informacoes do cluster:

`# kubectl config view`

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://191.168.0.111:6241
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```

2.  Agora vamos criar nosso contexto no Kubernetes:

```bash
# kubectl config set-context monitor --namespace=monitoring --user=kubernetes-admin --cluster=kubernetes
Context "monitor" created.
```

3.  Criando o outro contexto:

```bash
# kubectl config set-context observa --namespace=monitoring-prod --user=kubernetes-admin --cluster=kubernetes
Context "observa" created.
```

4.  Vamos conferir se esta correto nosso contexto:

`# kubectl config view`

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://191.168.0.111:6443      
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
- context:
    cluster: kubernetes
    namespace: monitoring
    user: kubernetes-admin
  name: monitor
- context:
    cluster: kubernetes
    namespace: monitoring-prod
    user: kubernetes-admin
  name: observa
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```

5.  Vamos alternar para o contexto `monitor`:

```bash
# kubectl config use-context monitor 
Switched to context "monitor".
```

6.  Para saber se estamos nesse contexto:

```bash
# kubectl config current-context 
monitor
```

7.  Veja que eu estou no ambiente correto:

```bash
# kubectl get all
NAME                           READY   STATUS    RESTARTS   AGE
pod/grafana-5c447577c9-pcpts   1/1     Running   0          78m

NAME              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/grafana   NodePort   10.104.31.49   <none>        3000:30059/TCP   77m

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana   1/1     1            1           78m

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-5c447577c9   1         1         1       78m
```
