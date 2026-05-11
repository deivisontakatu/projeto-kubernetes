# Projeto Docker — Tutorial Completo e Detalhado

# Sobre o Projeto

Este projeto demonstra como criar e executar uma aplicação simples utilizando Docker.

O objetivo principal é ensinar os conceitos fundamentais de containers e padronização de ambientes modernos utilizados em DevOps.

Ao final do tutorial você saberá:

- Criar aplicações containerizadas
- Criar imagens Docker
- Executar containers
- Mapear portas
- Resolver erros comuns
- Entender o fluxo básico de deploy com Docker

---

# Tecnologias Utilizadas

- Docker
- Docker Desktop
- Node.js
- JavaScript

---

# O que é Docker?

Docker é uma plataforma de virtualização leve baseada em containers.

Os containers permitem executar aplicações isoladas com todas as dependências necessárias.

Isso resolve problemas como:

```text
"Na minha máquina funciona."
```

Com Docker, o ambiente fica padronizado em qualquer computador ou servidor.

---

# Pré-requisitos

Antes de iniciar, instale:

## Docker Desktop

Download oficial:

https://www.docker.com/products/docker-desktop/

---

# Configuração Inicial do Docker

Após instalar:

1. Abra o Docker Desktop
2. Aguarde o Docker iniciar completamente
3. Verifique se aparece:

```text
Engine running
```

ou:

```text
Docker Desktop is running
```

⚠ IMPORTANTE:

Se o Docker Desktop estiver fechado, os comandos Docker não funcionarão.

---

# Problema Comum — Docker não inicia

Se aparecer erro como:

```text
failed to connect to the docker API
```

ou:

```text
docker daemon not running
```

faça:

1. Reinicie o Docker Desktop
2. Reinicie o computador
3. Verifique se o WSL2 está instalado
4. Ative virtualização na BIOS

---

# Verificando Instalação

Abra PowerShell ou terminal:

```bash
docker --version
```

Resultado esperado:

```bash
Docker version 29.x.x
```

---

# Verificando Engine Docker

Execute:

```bash
docker ps
```

Se não aparecer erro, o Docker está funcionando corretamente.

---

# Estrutura Final do Projeto

A estrutura correta será:

```text
projeto-docker/
│
├── app.js
└── Dockerfile
```

⚠ IMPORTANTE:

O arquivo deve chamar exatamente:

```text
Dockerfile
```

Sem:

```text
Dockerfile.txt
```

Esse é um dos erros mais comuns.

---

# Passo 1 — Criar Pasta do Projeto

Abra o terminal:

```bash
mkdir projeto-docker
cd projeto-docker
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

    res.end('Aplicacao Docker funcionando!');

});

server.listen(3000, () => {
    console.log('Servidor rodando na porta 3000');
});
```

---

# Explicação do Código

## require('http')

Importa módulo HTTP do Node.js.

---

## createServer()

Cria servidor web simples.

---

## res.writeHead()

Define tipo da resposta enviada.

---

## res.end()

Envia resposta para navegador.

---

## server.listen(3000)

Define que aplicação executará na porta 3000.

---

# Problema Comum — Porta incorreta

Se alterar:

```javascript
server.listen(3000)
```

também precisará alterar no Docker.

As portas precisam ser compatíveis.

---

# Passo 3 — Criar Dockerfile

Crie o arquivo:

```text
Dockerfile
```

⚠ MUITO IMPORTANTE:

No Windows, o bloco de notas pode salvar como:

```text
Dockerfile.txt
```

Isso causará erro:

```text
failed to read dockerfile
```

Verifique se o nome está correto.

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

## FROM node:20

Baixa imagem oficial do Node.js.

---

## WORKDIR /app

Cria diretório interno do container.

---

## COPY . .

Copia arquivos do projeto para container.

---

## EXPOSE 3000

Informa porta utilizada pela aplicação.

---

## CMD ["node", "app.js"]

Executa aplicação automaticamente.

---

# Passo 4 — Criar Imagem Docker

Execute:

```bash
docker build -t projeto-docker .
```

---

# Explicação do Comando

| Parte | Função |
|---|---|
| docker build | Cria imagem Docker |
| -t | Define nome da imagem |
| projeto-docker | Nome escolhido |
| . | Diretório atual |

---

# Problema Comum — Dockerfile não encontrado

Erro:

```text
failed to read dockerfile
```

Significa:

- Dockerfile inexistente
- Nome incorreto
- Arquivo salvo como `.txt`

---

# Verificando Imagens Criadas

Execute:

```bash
docker images
```

Resultado esperado:

```text
REPOSITORY         TAG       IMAGE ID
projeto-docker     latest    xxxxxxx
```

---

# Passo 5 — Executar Container

Execute:

```bash
docker run -p 3000:3000 projeto-docker
```

---

# Explicação do Comando

| Parte | Função |
|---|---|
| docker run | Executa container |
| -p | Faz mapeamento de portas |
| 3000:3000 | Porta local → container |
| projeto-docker | Nome imagem |

---

# Entendendo o Mapeamento de Portas

```text
3000:3000
```

significa:

```text
PORTA_PC : PORTA_CONTAINER
```

Exemplo:

```text
localhost:3000 → aplicação dentro container
```

---

# Problema Comum — Porta em Uso

Erro:

```text
Bind for 0.0.0.0:3000 failed
```

Significa que outra aplicação já usa porta 3000.

Solução:

```bash
docker run -p 3001:3000 projeto-docker
```

Depois acessar:

```text
http://localhost:3001
```

---

# Passo 6 — Testar Aplicação

Abra navegador:

```text
http://localhost:3000
```

Resultado esperado:

```text
Aplicacao Docker funcionando!
```

---

# Passo 7 — Ver Containers em Execução

Abra outro terminal:

```bash
docker ps
```

Resultado:

```text
CONTAINER ID   IMAGE              STATUS
xxxxxxxx       projeto-docker     Up
```

---

# Explicação do docker ps

Mostra:

- containers ativos
- portas utilizadas
- status execução
- imagens utilizadas

---

# Passo 8 — Parar Container

Copie CONTAINER ID:

```bash
docker stop ID_DO_CONTAINER
```

Exemplo:

```bash
docker stop 5ab123cd
```

---

# Passo 9 — Remover Container

```bash
docker rm ID_DO_CONTAINER
```

---

# Passo 10 — Remover Imagem Docker

```bash
docker rmi projeto-docker
```

---

# Conceitos Aprendidos

# Containers

Ambientes isolados para aplicações.

---

# Imagens Docker

Modelos reutilizáveis utilizados para criar containers.

---

# Portabilidade

A aplicação funciona igualmente em diferentes ambientes.

---

# Padronização

Todos ambientes possuem mesmas dependências.

---

# Fluxo do Projeto

```text
Aplicação Node.js
        ↓
Dockerfile
        ↓
Docker Build
        ↓
Imagem Docker
        ↓
Container
        ↓
Navegador
```

---

# Comandos Mais Importantes

## Ver imagens

```bash
docker images
```

---

## Ver containers ativos

```bash
docker ps
```

---

## Ver todos containers

```bash
docker ps -a
```

---

## Parar container

```bash
docker stop ID
```

---

## Remover container

```bash
docker rm ID
```

---

## Remover imagem

```bash
docker rmi NOME
```

---

# Problemas Mais Comuns

# Docker Desktop fechado

Erro:

```text
failed to connect to docker api
```

Solução:

- Abrir Docker Desktop

---

# Dockerfile incorreto

Erro:

```text
failed to read dockerfile
```

Solução:

- Verificar nome do arquivo

---

# Porta ocupada

Erro:

```text
port is already allocated
```

Solução:

- Trocar porta externa

---

# Aplicação não abre

Verifique:

- Docker Desktop aberto
- Container rodando
- Porta correta
- Navegador acessando localhost

---

# Melhorias Futuras

Você pode evoluir projeto para:

- Docker Compose
- Banco de Dados
- Kubernetes
- CI/CD
- Jenkins
- GitHub Actions
- Cloud Computing

---

# Conclusão

Docker simplifica criação de ambientes modernos através de containers isolados, padronizados e portáveis.

A tecnologia é amplamente utilizada em DevOps, Cloud Computing e microsserviços modernos.

---