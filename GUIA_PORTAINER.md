# 🚀 Guia: Como Subir Evolution API no Portainer

## 📋 Pré-requisitos
- Docker instalado
- Portainer configurado e rodando

---

## 🔧 Método 1: Via Stack no Portainer (RECOMENDADO)

### Passo 1: Acesse o Portainer
1. Abra seu navegador
2. Acesse o Portainer (geralmente: `http://localhost:9000` ou `http://seu-ip:9000`)
3. Faça login

### Passo 2: Criar uma Nova Stack
1. No menu lateral, clique em **"Stacks"**
2. Clique no botão **"+ Add stack"**
3. Dê um nome para a stack: `evolution-api`

### Passo 3: Cole o Docker Compose
1. Na aba **"Web editor"**, copie e cole o conteúdo do arquivo:
   - Arquivo: `docker-compose.portainer.yaml`

2. Ou copie diretamente este conteúdo:

```yaml
version: "3.8"

services:
  api:
    container_name: evolution_api
    image: evoapicloud/evolution-api:latest
    restart: always
    depends_on:
      - redis
      - evolution-postgres
    ports:
      - "8080:8080"
    volumes:
      - evolution_instances:/evolution/instances
    networks:
      - evolution-net
    environment:
      - SERVER_NAME=evolution
      - SERVER_TYPE=http
      - SERVER_PORT=8080
      - SERVER_URL=http://localhost:8080
      - TELEMETRY_ENABLED=false
      - CORS_ORIGIN=*
      - CORS_METHODS=GET,POST,PUT,DELETE
      - CORS_CREDENTIALS=true
      - LOG_LEVEL=ERROR,WARN,DEBUG,INFO,LOG,VERBOSE,DARK,WEBHOOKS
      - LOG_COLOR=true
      - LOG_BAILEYS=error
      - DEL_INSTANCE=false
      - DATABASE_PROVIDER=postgresql
      - DATABASE_CONNECTION_URI=postgresql://postgres:evolution123@evolution-postgres:5432/evolution_db?schema=evolution_api
      - DATABASE_CONNECTION_CLIENT_NAME=evolution_exchange
      - DATABASE_SAVE_DATA_INSTANCE=true
      - DATABASE_SAVE_DATA_NEW_MESSAGE=true
      - DATABASE_SAVE_MESSAGE_UPDATE=true
      - DATABASE_SAVE_DATA_CONTACTS=true
      - DATABASE_SAVE_DATA_CHATS=true
      - DATABASE_SAVE_DATA_LABELS=true
      - DATABASE_SAVE_DATA_HISTORIC=true
      - CACHE_REDIS_ENABLED=true
      - CACHE_REDIS_URI=redis://redis:6379/6
      - CACHE_REDIS_TTL=604800
      - CACHE_REDIS_PREFIX_KEY=evolution
      - CACHE_REDIS_SAVE_INSTANCES=false
      - CACHE_LOCAL_ENABLED=false
      - AUTHENTICATION_API_KEY=429683C4C977415CAAFCCE10F7D57E11
      - AUTHENTICATION_EXPOSE_IN_FETCH_INSTANCES=true
      - LANGUAGE=pt-BR
      - CONFIG_SESSION_PHONE_CLIENT=Evolution API
      - CONFIG_SESSION_PHONE_NAME=Chrome
      - QRCODE_LIMIT=30
      - QRCODE_COLOR=#175197
      - RABBITMQ_ENABLED=false
      - SQS_ENABLED=false
      - WEBSOCKET_ENABLED=false
      - PUSHER_ENABLED=false
      - KAFKA_ENABLED=false
      - WEBHOOK_GLOBAL_ENABLED=false
      - TYPEBOT_ENABLED=false
      - CHATWOOT_ENABLED=false
      - OPENAI_ENABLED=false
      - DIFY_ENABLED=false
      - N8N_ENABLED=false
      - EVOAI_ENABLED=false
      - S3_ENABLED=false
      - WA_BUSINESS_TOKEN_WEBHOOK=evolution
      - WA_BUSINESS_URL=https://graph.facebook.com
      - WA_BUSINESS_VERSION=v20.0
      - WA_BUSINESS_LANGUAGE=pt_BR

  frontend:
    container_name: evolution_frontend
    image: evoapicloud/evolution-manager:latest
    restart: always
    ports:
      - "3000:80"
    networks:
      - evolution-net
    environment:
      - REACT_APP_API_URL=http://localhost:8080

  redis:
    container_name: evolution_redis
    image: redis:latest
    restart: always
    command: redis-server --port 6379 --appendonly yes
    volumes:
      - evolution_redis:/data
    networks:
      - evolution-net

  evolution-postgres:
    container_name: evolution_postgres
    image: postgres:15
    restart: always
    command:
      - postgres
      - -c
      - max_connections=1000
      - -c
      - listen_addresses=*
    environment:
      - POSTGRES_DB=evolution_db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=evolution123
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - evolution-net

volumes:
  evolution_instances:
  evolution_redis:
  postgres_data:

networks:
  evolution-net:
    name: evolution-net
    driver: bridge
```

### Passo 4: Deploy da Stack
1. Role até o final da página
2. Clique no botão **"Deploy the stack"**
3. Aguarde o Portainer baixar as imagens e criar os containers

### Passo 5: Verificar Status
1. Clique em **"Stacks"** no menu lateral
2. Clique na stack **"evolution-api"**
3. Verifique se todos os containers estão com status **"running"** (verde)

---

## 🔧 Método 2: Via Upload de Arquivo

### Passo 1: Preparar o Arquivo
1. Localize o arquivo: `docker-compose.portainer.yaml`
2. Certifique-se de que está salvo no seu computador

### Passo 2: Criar Stack com Upload
1. Acesse **Portainer** → **Stacks** → **+ Add stack**
2. Nome da stack: `evolution-api`
3. Selecione a aba **"Upload"**
4. Clique em **"Select file"**
5. Selecione o arquivo `docker-compose.portainer.yaml`
6. Clique em **"Deploy the stack"**

---

## ✅ Verificar se está funcionando

### 1. Verificar Containers
No Portainer:
- Vá em **Containers**
- Você deve ver 4 containers rodando:
  - ✅ `evolution_api`
  - ✅ `evolution_frontend`
  - ✅ `evolution_redis`
  - ✅ `evolution_postgres`

### 2. Acessar a API
Abra o navegador:
- API: http://localhost:8080
- Frontend (Manager): http://localhost:3000

### 3. Testar a API
Execute este comando ou use o Postman/Insomnia:
```bash
curl -X GET http://localhost:8080/
```

Ou acesse diretamente no navegador:
```
http://localhost:8080
```

---

## 🔑 Informações de Acesso

### API Key (Authentication)
```
429683C4C977415CAAFCCE10F7D57E11
```

Use esta chave no header das requisições:
```
apikey: 429683C4C977415CAAFCCE10F7D57E11
```

### Banco de Dados PostgreSQL
```
Host: evolution-postgres (ou localhost se acessar de fora)
Porta: 5432
Database: evolution_db
Usuário: postgres
Senha: evolution123
```

### Redis
```
Host: evolution_redis (ou localhost se acessar de fora)
Porta: 6379
```

---

## 📝 Criar uma Instância do WhatsApp

### Via API:
```bash
curl -X POST http://localhost:8080/instance/create \
  -H "Content-Type: application/json" \
  -H "apikey: 429683C4C977415CAAFCCE10F7D57E11" \
  -d '{
    "instanceName": "minha_instancia",
    "qrcode": true
  }'
```

### Via Frontend (Manager):
1. Acesse: http://localhost:3000
2. Configure a API URL: `http://localhost:8080`
3. Insira a API Key: `429683C4C977415CAAFCCE10F7D57E11`
4. Crie uma nova instância
5. Escaneie o QR Code com seu WhatsApp

---

## 🛠️ Comandos Úteis no Portainer

### Ver Logs de um Container
1. Vá em **Containers**
2. Clique no container desejado (ex: `evolution_api`)
3. Clique na aba **"Logs"**

### Reiniciar um Container
1. Vá em **Containers**
2. Selecione o container
3. Clique em **"Restart"**

### Parar a Stack
1. Vá em **Stacks**
2. Selecione `evolution-api`
3. Clique em **"Stop this stack"**

### Remover a Stack
1. Vá em **Stacks**
2. Selecione `evolution-api`
3. Clique em **"Delete this stack"**
4. ⚠️ **ATENÇÃO**: Marque "Remove volumes" apenas se quiser **DELETAR TODOS OS DADOS**

---

## 🔄 Atualizar a Aplicação

### Método 1: Via Portainer
1. Vá em **Stacks** → `evolution-api`
2. Clique em **"Editor"**
3. Na parte inferior, clique em **"Pull and redeploy"**

### Método 2: Forçar Pull da Imagem
1. Vá em **Images**
2. Procure por `evoapicloud/evolution-api:latest`
3. Clique em **"Pull image"**
4. Depois vá em **Stacks** e clique em **"Update the stack"**

---

## 🐛 Troubleshooting

### Container não inicia
1. Verifique os logs do container
2. Verifique se as portas 8080, 3000, 5432 e 6379 não estão em uso

### Erro de conexão com banco de dados
1. Verifique se o container `evolution_postgres` está rodando
2. Verifique os logs do PostgreSQL
3. Aguarde alguns segundos após o start (banco demora um pouco para iniciar)

### QR Code não aparece
1. Verifique os logs do container `evolution_api`
2. Certifique-se de que a instância foi criada corretamente
3. Tente recriar a instância

---

## 📚 Documentação Oficial

- Evolution API: https://doc.evolution-api.com/
- Portainer: https://docs.portainer.io/

---

## ✨ Pronto!

Sua Evolution API está rodando no Portainer! 🎉

Se tiver alguma dúvida, consulte a documentação oficial ou verifique os logs dos containers.
