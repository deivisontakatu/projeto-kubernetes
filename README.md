# Projeto Kubernetes — Tutorial Completo

# Sobre o Projeto

Este projeto demonstra como executar uma aplicação Node.js utilizando Kubernetes para orquestração de containers.

O objetivo é apresentar conceitos fundamentais de:

- Containers
- Kubernetes
- Pods
- Deployments
- Services
- Escalabilidade
- Orquestração de aplicações

---

# Tecnologias Utilizadas

- Docker
- Kubernetes
- Node.js
- JavaScript
- kubectl

---

# O que é Kubernetes?

Kubernetes é uma plataforma de orquestração de containers responsável pelo gerenciamento automatizado de aplicações modernas.

Com Kubernetes é possível:

- Automatizar deploys
- Escalar aplicações
- Reiniciar containers automaticamente
- Balancear carga
- Garantir alta disponibilidade

---

# Pré-requisitos

Antes de iniciar, instale:

## Docker Desktop

Download oficial:

https://www.docker.com/products/docker-desktop/

⚠ IMPORTANTE:

Durante a instalação:

- habilite WSL2
- reinicie o computador se solicitado

---

# Habilitando Kubernetes no Docker Desktop

Abra:

```text
Docker Desktop → Settings → Kubernetes
```

Ative:

```text
Enable Kubernetes
```

Depois clique em:

```text
Apply & Restart
```

⚠ Aguarde até aparecer:

```text
Kubernetes running
```

Esse processo pode levar alguns minutos.

---

# Verificando Kubernetes

Abra o terminal:

```bash
kubectl version
```

---

# Testando Cluster

Execute:

```bash
kubectl get nodes
```

Resultado esperado:

```text
NAME             STATUS   ROLES           AGE
docker-desktop   Ready    control-plane
```

⚠ Se aparecer erro:

```text
connection refused
```

aguarde Kubernetes finalizar inicialização no Docker Desktop.

---

# Estrutura do Projeto

```text
projeto-kubernetes/
│
├── app.js
├── Dockerfile
├── deployment.yaml
└── service.yaml
```

---

# Passo 1 — Criar Pasta do Projeto

```bash
mkdir projeto-kubernetes
cd projeto-kubernetes
```

---

# Passo 2 — Criar Aplicação Node.js

Crie o arquivo:

```text
app.js
```

Conteúdo:

```javascript
const http = require('http');

const server = http.createServer((req, res) => {

    res.writeHead(200, {
        'Content-Type': 'text/plain'
    });

    res.end('Aplicacao Kubernetes funcionando!');

});

server.listen(3000, () => {
    console.log('Servidor rodando na porta 3000');
});
```

---

# Explicação do Código

| Método | Função |
|---|---|
| createServer() | Cria servidor HTTP |
| writeHead() | Define cabeçalho HTTP |
| res.end() | Retorna resposta |
| listen(3000) | Executa aplicação |

---

# Passo 3 — Criar Dockerfile

Crie o arquivo:

```text
Dockerfile
```

⚠ IMPORTANTE:

O arquivo deve chamar exatamente:

```text
Dockerfile
```

e NÃO:

```text
Dockerfile.txt
```

---

# Conteúdo do Dockerfile

```dockerfile
FROM node:20

WORKDIR /app

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

# Explicação do Dockerfile

| Comando | Função |
|---|---|
| FROM | Imagem base Node.js |
| WORKDIR | Diretório interno |
| COPY | Copia arquivos |
| EXPOSE | Expõe porta |
| CMD | Executa aplicação |

---

# Passo 4 — Criar Imagem Docker

Execute:

```bash
docker build -t projeto-kubernetes:latest .
```

⚠ IMPORTANTE:

Utilize:

```text
:latest
```

para evitar problemas de versão no Kubernetes.

---

# Verificando Imagem

```bash
docker images
```

Resultado esperado:

```text
REPOSITORY             TAG       IMAGE ID
projeto-kubernetes     latest    xxxxxxx
```

---

# Passo 5 — Criar Deployment Kubernetes

Crie:

```text
deployment.yaml
```

Conteúdo:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: projeto-kubernetes

spec:
  replicas: 2

  selector:
    matchLabels:
      app: projeto-kubernetes

  template:
    metadata:
      labels:
        app: projeto-kubernetes

    spec:
      containers:
      - name: projeto-kubernetes
        image: projeto-kubernetes:latest

        imagePullPolicy: IfNotPresent

        ports:
        - containerPort: 3000
```

---

# Explicação do Deployment

| Campo | Função |
|---|---|
| replicas | Quantidade de containers |
| image | Imagem Docker utilizada |
| containerPort | Porta interna |
| imagePullPolicy | Política de download |

---

# IMPORTANTE — imagePullPolicy

Utilize:

```yaml
imagePullPolicy: IfNotPresent
```

Evita erro:

```text
ErrImageNeverPull
```

muito comum no Docker Desktop Windows.

---

# Passo 6 — Criar Service Kubernetes

Crie:

```text
service.yaml
```

Conteúdo:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: projeto-kubernetes-service

spec:
  type: NodePort

  selector:
    app: projeto-kubernetes

  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30080
```

---

# Explicação do Service

| Campo | Função |
|---|---|
| NodePort | Expõe aplicação |
| port | Porta serviço |
| targetPort | Porta container |
| nodePort | Porta navegador |

---

# Passo 7 — Aplicar Deployment

Execute:

```bash
kubectl apply -f deployment.yaml
```

Resultado esperado:

```text
deployment.apps/projeto-kubernetes created
```

---

# Passo 8 — Aplicar Service

```bash
kubectl apply -f service.yaml
```

Resultado esperado:

```text
service/projeto-kubernetes-service created
```

---

# Passo 9 — Verificar Pods

```bash
kubectl get pods
```

Resultado esperado:

```text
NAME                                  READY   STATUS
projeto-kubernetes-xxxxx              1/1     Running
projeto-kubernetes-yyyyy              1/1     Running
```

⚠ IMPORTANTE:

Se aparecer:

```text
ContainerCreating
```

aguarde alguns segundos.

---

# Problema Comum — ErrImageNeverPull

Se aparecer:

```text
ErrImageNeverPull
```

verifique:

- imagem criada corretamente
- nome imagem igual deployment
- uso de `:latest`
- `imagePullPolicy: IfNotPresent`

---

# Passo 10 — Verificar Services

```bash
kubectl get services
```

Resultado esperado:

```text
NAME                         TYPE       PORT(S)
projeto-kubernetes-service   NodePort   3000:30080/TCP
```

---

# Passo 11 — Testar Aplicação

Tente acessar:

```text
http://localhost:30080
```

---

# Problema Comum — Porta não abre

No Windows com Docker Desktop, o NodePort pode não funcionar diretamente.

Solução:

Execute:

```bash
kubectl port-forward service/projeto-kubernetes-service 3000:3000
```

Resultado esperado:

```text
Forwarding from 127.0.0.1:3000 -> 3000
```

⚠ NÃO feche esse terminal.

Depois abra:

```text
http://localhost:3000
```

---

# Conceitos Aprendidos

# Pods

Menor unidade executável do Kubernetes.

---

# Deployments

Gerenciam containers automaticamente.

---

# Services

Expõem aplicações na rede.

---

# Escalabilidade

Permite aumentar quantidade de containers facilmente.

---

# Alta Disponibilidade

Mantém aplicação funcionando mesmo com falhas.

---

# Escalando Aplicação

Altere:

```yaml
replicas: 2
```

para:

```yaml
replicas: 5
```

Depois execute:

```bash
kubectl apply -f deployment.yaml
```

Verifique:

```bash
kubectl get pods
```

---

# Atualizando Aplicação

Altere:

```javascript
res.end('Nova versao Kubernetes!');
```

Depois execute:

```bash
docker build -t projeto-kubernetes:latest .
```

Reinicie deployment:

```bash
kubectl rollout restart deployment projeto-kubernetes
```

---

# Verificando Logs

```bash
kubectl logs NOME_DO_POD
```

---

# Entrando no Container

```bash
kubectl exec -it NOME_DO_POD -- sh
```

---

# Removendo Recursos

## Remover deployment

```bash
kubectl delete -f deployment.yaml
```

---

## Remover service

```bash
kubectl delete -f service.yaml
```

---

# Conceitos DevOps Aplicados

- Containers
- Orquestração
- Escalabilidade
- Deploy automatizado
- Alta disponibilidade
- Infraestrutura moderna

---

# Melhorias Futuras

Este projeto pode evoluir para:

- Banco de Dados
- Helm
- Ingress
- CI/CD
- Jenkins
- GitHub Actions
- Monitoramento
- Cloud Kubernetes
- Microserviços

---

# Conclusão

Kubernetes automatiza gerenciamento de containers e facilita escalabilidade, disponibilidade e manutenção de aplicações modernas.

A combinação Docker + Kubernetes é amplamente utilizada em ambientes DevOps, Cloud Computing e microsserviços.

---
