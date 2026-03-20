# Quick Start - n8n Self-Hosted AI Starter Kit

## Pre-requisitos

- [Docker](https://docs.docker.com/get-docker/) e [Docker Compose](https://docs.docker.com/compose/install/) instalados
- Git

## 1. Clonar o repositorio

```bash
git clone https://github.com/n8n-io/self-hosted-ai-starter-kit.git
cd self-hosted-ai-starter-kit
```

## 2. Configurar variaveis de ambiente

```bash
cp .env.example .env
```

Edite o `.env` e altere os valores padroes (senhas, chaves, etc.):

| Variavel                          | Descricao                        |
| --------------------------------- | -------------------------------- |
| `POSTGRES_USER`                   | Usuario do PostgreSQL            |
| `POSTGRES_PASSWORD`               | Senha do PostgreSQL              |
| `POSTGRES_DB`                     | Nome do banco de dados           |
| `N8N_ENCRYPTION_KEY`              | Chave de criptografia do n8n     |
| `N8N_USER_MANAGEMENT_JWT_SECRET`  | Secret JWT do n8n                |

## 3. Subir a aplicacao

Escolha o comando de acordo com seu hardware:

**Mac / Apple Silicon (sem GPU no Docker):**

```bash
docker compose up -d
```

**CPU apenas (Linux/Windows):**

```bash
docker compose --profile cpu up -d
```

**GPU Nvidia:**

```bash
docker compose --profile gpu-nvidia up -d
```

**GPU AMD (Linux):**

```bash
docker compose --profile gpu-amd up -d
```

> Na primeira execucao, o Docker fara o build da imagem customizada do n8n (com Python incluso) e o download do modelo Llama 3.2 via Ollama. Isso pode levar alguns minutos.

## 4. Acessar o n8n

Abra no navegador: **http://localhost:5678/**

Na primeira vez, crie sua conta de administrador.

## 5. Servicos disponiveis

| Servico    | URL                          |
| ---------- | ---------------------------- |
| n8n        | http://localhost:5678        |
| Ollama     | http://localhost:11434       |
| Qdrant     | http://localhost:6333        |

## 6. PostgreSQL com pgvector

O PostgreSQL ja sobe com a extensao **pgvector** habilitada automaticamente (via init script em `postgres/init/`).

Para verificar se a extensao esta ativa, conecte no banco e execute:

```bash
docker compose exec postgres psql -U ${POSTGRES_USER} -d ${POSTGRES_DB} -c "\dx"
```

Caso precise habilitar manualmente em outro banco:

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

## 7. Mac com Ollama local

Se voce roda o Ollama direto no Mac (fora do Docker):

1. No `.env`, descomente e configure:
   ```
   OLLAMA_HOST=host.docker.internal:11434
   ```
2. Suba sem profile: `docker compose up -d`
3. No n8n, va em **Credentials > Local Ollama service** e altere a base URL para `http://host.docker.internal:11434/`

## 8. Parar a aplicacao

```bash
docker compose down
```

Para remover tambem os volumes (dados do banco, modelos, etc.):

```bash
docker compose down -v
```

## 9. Atualizar

```bash
docker compose pull
docker compose build
docker compose up -d
```

## Estrutura de pastas

```
.
├── docker-compose.yml     # Orquestracao dos servicos
├── .env                   # Variaveis de ambiente (nao commitado)
├── n8n/
│   ├── Dockerfile         # Imagem customizada do n8n (com Python)
│   └── demo-data/         # Workflows e credentials de demo
├── postgres/
│   └── init/              # Scripts SQL executados na inicializacao (pgvector)
└── shared/                # Pasta compartilhada com o container (/data/shared)
```
