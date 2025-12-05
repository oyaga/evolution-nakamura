# 🚀 Como Fazer Deploy da Evolution API com as Correções de Botões

## 📝 Resumo das Correções Aplicadas

Foram corrigidos **2 problemas críticos** que impediam os botões de chegarem no WhatsApp:

1. **`contextInfo` não aplicado em mensagens interativas** - Arquivo: [whatsapp.baileys.service.ts:1998-2026](src/api/integrations/channel/whatsapp/whatsapp.baileys.service.ts#L1998-L2026)
2. **Tipagem incorreta nas mensagens de contato** - Arquivo: [whatsapp.baileys.service.ts:3355-3364](src/api/integrations/channel/whatsapp/whatsapp.baileys.service.ts#L3355-L3364)

---

## 🎯 Opção 1: Build e Deploy da Imagem Customizada (RECOMENDADO)

### Passo 1: Fazer Build da Imagem com as Correções

No seu servidor ou máquina local:

```bash
cd c:\Users\kirap\Desktop\evolution-api-main\evolution-api-main

# Fazer build da imagem customizada
docker build -t evolution-api-custom:latest .
```

### Passo 2: Fazer Push para um Registry (Opcional)

Se você usar Docker Hub ou um registry privado:

```bash
# Login no Docker Hub
docker login

# Tag da imagem
docker tag evolution-api-custom:latest SEU_USUARIO/evolution-api-custom:latest

# Push da imagem
docker push SEU_USUARIO/evolution-api-custom:latest
```

### Passo 3: Atualizar o Docker Compose

Edite o arquivo `docker-compose.production.yaml` e **descomente** estas linhas:

```yaml
evolution_api:
  ## IMPORTANTE: Use a imagem custom após o build com as correções de botões:
  build: .
  image: evolution-api-custom:latest
  ## OU se fez push para o registry:
  ## image: SEU_USUARIO/evolution-api-custom:latest
```

Remova ou comente a linha:
```yaml
  # image: evoapicloud/evolution-api:latest ## Comentar esta linha
```

### Passo 4: Deploy no Docker Swarm

```bash
# Deploy da stack
docker stack deploy -c docker-compose.production.yaml evolution

# Verificar status
docker stack ps evolution

# Ver logs
docker service logs evolution_evolution_api -f
```

---

## 🎯 Opção 2: Deploy no Portainer com Imagem Customizada

### Passo 1: Build Local

```bash
cd c:\Users\kirap\Desktop\evolution-api-main\evolution-api-main
docker build -t evolution-api-custom:latest .
```

### Passo 2: No Portainer

1. Acesse **Portainer** → **Stacks**
2. Se já existe uma stack `evolution-api`, **remova-a** (⚠️ faça backup dos dados!)
3. Clique em **+ Add stack**
4. Nome: `evolution-api`
5. Cole o conteúdo de `docker-compose.production.yaml`
6. **Modifique a imagem** para:
   ```yaml
   evolution_api:
     image: evolution-api-custom:latest
   ```
7. Clique em **Deploy the stack**

---

## 🎯 Opção 3: Atualizar Apenas o Código (Sem Rebuild)

Se você quiser apenas atualizar o código sem fazer rebuild completo:

### Passo 1: Copiar os Arquivos Corrigidos

```bash
# No container em execução
docker cp src/api/integrations/channel/whatsapp/whatsapp.baileys.service.ts evolution_api:/evolution/src/api/integrations/channel/whatsapp/

# Rebuild dentro do container
docker exec -it evolution_api npm run build
```

### Passo 2: Reiniciar o Container

```bash
docker restart evolution_api
```

⚠️ **ATENÇÃO**: Esta opção **NÃO é recomendada** para produção, pois as mudanças serão perdidas se o container for recriado!

---

## 📦 Opção 4: Deploy Rápido no Localhost (Teste)

Para testar localmente antes de fazer deploy em produção:

### Usar o docker-compose.portainer.yaml

```bash
cd c:\Users\kirap\Desktop\evolution-api-main\evolution-api-main

# Fazer build da imagem customizada
docker build -t evolution-api-custom:latest .

# Editar docker-compose.portainer.yaml
# Mudar de:
#   image: evoapicloud/evolution-api:latest
# Para:
#   image: evolution-api-custom:latest

# Subir os containers
docker compose -f docker-compose.portainer.yaml up -d

# Ver logs
docker compose -f docker-compose.portainer.yaml logs -f api
```

Acesse:
- API: http://localhost:8080
- Frontend: http://localhost:3000

---

## 🧪 Testar as Correções de Botões

### 1. Via API (cURL)

```bash
curl -X POST http://localhost:8080/message/sendButtons/Aika \
  -H "Content-Type: application/json" \
  -H "apikey: 988aeb4c9250361977ddacd0fdb1ba1e" \
  -d '{
    "number": "5519993369603",
    "title": "Teste de Botões",
    "description": "Selecione uma opção",
    "footer": "Evolution API",
    "buttons": [
      {
        "type": "reply",
        "displayText": "Opção 1",
        "id": "opcao_1"
      },
      {
        "type": "reply",
        "displayText": "Opção 2",
        "id": "opcao_2"
      }
    ]
  }'
```

### 2. Via N8N

Use a configuração que forneci anteriormente:

```json
{
  "parameters": {
    "resource": "messages-api",
    "operation": "send-buttons",
    "instanceName": "Aika",
    "number": "5519993369603",
    "title": "Escolha uma opção",
    "description": "Selecione uma das opções abaixo",
    "footer": "Evolution API",
    "buttons": [
      {
        "type": "reply",
        "displayText": "Opção 1",
        "id": "opcao_1"
      },
      {
        "type": "reply",
        "displayText": "Opção 2",
        "id": "opcao_2"
      }
    ]
  }
}
```

---

## 🔍 Verificar se as Correções Estão Ativas

### Método 1: Verificar Logs

```bash
# Ver logs do container
docker logs evolution_api -f

# Quando enviar botões, você deve ver:
# "Sending message to 5519993369603@s.whatsapp.net"
# E não deve ter erros relacionados a contextInfo
```

### Método 2: Verificar Código no Container

```bash
# Entrar no container
docker exec -it evolution_api sh

# Verificar se o arquivo tem as correções
cat /evolution/dist/api/integrations/channel/whatsapp/whatsapp.baileys.service.js | grep -A 10 "contextInfo"

# Deve aparecer o código corrigido
```

---

## 📊 Comparação das Opções

| Opção | Dificuldade | Tempo | Recomendado Para |
|-------|-------------|-------|------------------|
| **Opção 1** | Média | 10-20 min | Produção com CI/CD |
| **Opção 2** | Fácil | 5-10 min | Portainer simples |
| **Opção 3** | Fácil | 2-5 min | Teste rápido (⚠️ temporário) |
| **Opção 4** | Fácil | 5-10 min | Desenvolvimento/Teste local |

---

## ⚠️ Checklist Antes do Deploy

- [ ] Fazer backup do banco de dados PostgreSQL
- [ ] Fazer backup dos volumes (evolution_instances)
- [ ] Testar localmente primeiro (Opção 4)
- [ ] Verificar se as redes Docker existem (AikaNet)
- [ ] Verificar se os volumes externos existem
- [ ] Documentar a versão atual da imagem
- [ ] Ter um plano de rollback

---

## 🔄 Rollback em Caso de Problemas

Se algo der errado, você pode voltar para a versão anterior:

```bash
# Via Docker Swarm
docker service update --image evoapicloud/evolution-api:latest evolution_evolution_api

# Via Docker Compose
# Editar docker-compose e voltar para:
# image: evoapicloud/evolution-api:latest
docker compose up -d --force-recreate api
```

---

## 📚 Arquivos Importantes

- `docker-compose.production.yaml` - Configuração para Docker Swarm (sua produção)
- `docker-compose.portainer.yaml` - Configuração simplificada para Portainer
- `Dockerfile` - Build da imagem customizada
- `.env` - Variáveis de ambiente locais
- `src/api/integrations/channel/whatsapp/whatsapp.baileys.service.ts` - Arquivo com as correções

---

## 🆘 Troubleshooting

### Botões ainda não chegam no WhatsApp

1. Verifique os logs:
   ```bash
   docker logs evolution_api -f
   ```

2. Confirme que está usando a imagem customizada:
   ```bash
   docker inspect evolution_api | grep Image
   ```

3. Teste com um número diferente

### Container não inicia

1. Verifique se o PostgreSQL está rodando
2. Verifique se o Redis está rodando
3. Verifique as variáveis de ambiente

### Build falha

1. Certifique-se de que tem Node.js instalado
2. Execute `npm install` antes do build
3. Verifique se gerou o Prisma Client:
   ```bash
   npm run db:generate
   ```

---

## ✅ Sucesso!

Se tudo correu bem:
- ✅ Containers rodando
- ✅ API respondendo em https://evolution.aikanakamura.com
- ✅ Botões chegando corretamente no WhatsApp
- ✅ Logs sem erros

---

## 🎉 Pronto!

Sua Evolution API está rodando com as correções de botões!

Para dúvidas ou problemas, verifique os logs ou entre em contato com o suporte.
