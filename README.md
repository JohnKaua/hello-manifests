# Documentação hello-manifests

## Visão geral

Repositório Git usado pelo **ArgoCD** para sincronizar e implantar a aplicação `hello-app` no cluster Kubernetes.  
Este repositório segue o modelo **GitOps**, onde toda alteração nos manifests reflete automaticamente em um deploy atualizado.

## Arquivos principais

### `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
  labels:
    app: hello-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-app
  template:
    metadata:
      labels:
        app: hello-app
    spec:
      containers:
      - name: hello-app
        image: johnkaua/hello-app:latest
        ports:
        - containerPort: 80
```

### `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-app
spec:
  type: ClusterIP
  selector:
    app: hello-app
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
```

## Fluxo GitOps com ArgoCD

1. O pipeline do repositório `hello-app` faz o push de uma nova imagem no Docker Hub.  
2. O workflow cria automaticamente um **Pull Request** neste repositório, atualizando o campo `image:` com a nova tag.  
3. Após o merge, o **ArgoCD detecta a mudança** e sincroniza o novo deploy.  
4. O cluster Kubernetes é atualizado automaticamente.

## Configuração no ArgoCD

- **Repository URL**:  `https://github.com/SEU_USERNAME/hello-manifests`:
- **Path**:  `.`
- **Cluster**:  `in-cluster` (Rancher Desktop)
- **Namespace**: `default`
- **Sync Policy**:  Automática (Auto-Sync) ou Manual

## Benefícios

- Deploy automatizado sem comandos `kubectl`
- Histórico versionado de cada atualização
- Integração completa com o pipeline CI/CD
