# Services

### Create vs Apply

1.  *Create*: usado dentro da abordagem de `Imperative Management`.
    1.  você informa à API do Kubernetes o que deseja criar, substituir ou excluir.
   
2.  *Apply*: usado dentro da abordagem de `Declarative Management`.
    1.  abordagem onde as alterações que você pode ter aplicado a um objeto ao vivo (ou seja, através de escala) são "mantidas" mesmo se você aplicar outras alterações ao objeto.

3.  Vamos aos exemplos abaixo:
    1.  Criando um deployment com a imagem do `NGNX`:

```bash
# kubectl create deployment nginx --image=nginx
deployment.apps/nginx create
```
