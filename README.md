# kind - Criando seu cluster k8s + registry local

Repositorio com os passos necessários para criação de um cluster com kind(https://kind.sigs.k8s.io/).

# Introdução

Para criação do cluster com o kind, utilizar o comando abaixo:

```kind create cluster --config kind-ingress-volumes-registry.yml --name dev```

Caso queira utilizar o ingress controller(NGINX), utilizar o comando abaixo:

```kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml```

É necessário que aguardar um pouco até a conlusão do processo. É possivel utilizar o comando abaixo para verificar se tudo está concluido:
```shell
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s
```
Necessário també, incluir no /etc/docker/daemon.json o conteúdo abaixo:

```json
{
  "insecure-registries" : ["localhost:5000","registry.local:5000"]
}
```
Para subir o registry que será utilizado para publicação das imagens utilizar o comando abaixo:

```docker run -d -p 5000:5000 --name registry --add-host registry.local:192.168.1.66 registry:2```

Altere o IP acima pelo seu IP local. Também será necessário adicionar o nome acima ao /etc/hosts.

Ex:
```shell
192.168.1.66 registry.local
```
Importante lembrar que sua imagem precisará receber a tag no seguinte formato:

```docker tag api:1.0 registry.local:5000/api:1.0```

Para realizar o push da imagem basta utilizar:

```docker push registry.local:5000/api:1.0```

Em seu arquivo de deployment será necessário realizar uma alteração também para referenciar o endereço IP da sua maquina local:

```yaml
image: registry.local:5000/api:1.0
```
