# 🔄 Atualizar Stack no Portainer com Novo Domínio

## 🎯 Novo Domínio
**evolution-teste.aikanakamura.com**

---

## 📋 Passo a Passo - Atualizar Stack Existente

### **Opção 1: Atualizar Stack Existente no Portainer**

1. **Acesse o Portainer**
   - URL: `http://IP_VPS:9000` ou `https://portainer.seudominio.com`

2. **Vá em Stacks**
   - Menu lateral → **Stacks**
   - Clique na stack `evolution` (ou o nome que você deu)

3. **Editar a Stack**
   - Clique no botão **"Editor"**
   - Você verá o YAML atual

4. **Atualizar o Domínio**

   Procure e substitua estas linhas:

   **ANTES:**
   ```yaml
   - SERVER_URL=https://evolution.aikanakamura.com
   ```

   **DEPOIS:**
   ```yaml
   - SERVER_URL=https://evolution-teste.aikanakamura.com
   ```

   **ANTES:**
   ```yaml
   - traefik.http.routers.evolution.rule=Host(`evolution.aikanakamura.com`)
   - traefik.http.routers.evolution.entrypoints=websecure
   - traefik.http.routers.evolution.tls.certresolver=letsencryptresolver
   - traefik.http.services.evolution.loadbalancer.server.port=8080
   ```

   **DEPOIS:**
   ```yaml
   - traefik.http.routers.evolution-teste.rule=Host(`evolution-teste.aikanakamura.com`)
   - traefik.http.routers.evolution-teste.entrypoints=websecure
   - traefik.http.routers.evolution-teste.tls.certresolver=letsencryptresolver
   - traefik.http.services.evolution-teste.loadbalancer.server.port=8080
   ```

5. **Aplicar as Mudanças**
   - Role até o final da página
   - Clique em **"Update the stack"**
   - Aguarde o Portainer reiniciar os containers

---

### **Opção 2: Criar Nova Stack com Arquivo Atualizado**

1. **Na VPS - Atualizar o Repositório**
   ```bash
   cd ~/evolution-nakamura
   git pull origin master
   ```

2. **No Portainer**
   - Vá em **Stacks** → **+ Add stack**
   - Nome: `evolution-teste`
   - Copie o conteúdo do arquivo: `stack-portainer-evolution-teste.yaml`
   - Ou use o **Upload** e suba o arquivo
   - Clique em **Deploy the stack**

---

### **Opção 3: Via Terminal (SSH) - Usando Docker Swarm**

Se você estiver usando Docker Swarm:

```bash
# Na VPS
cd ~/evolution-nakamura

# Atualizar repositório
git pull origin master

# Deploy da stack atualizada
docker stack deploy -c docker-compose.production.yaml evolution

# Verificar status
docker stack ps evolution
```

---

## 🔧 Configurar DNS

**IMPORTANTE**: Configure o DNS do novo domínio antes de acessar!

### **No seu provedor de DNS (Cloudflare, etc):**

1. Acesse o painel do seu provedor
2. Adicione um registro do tipo **A**:
   - **Nome**: `evolution-teste`
   - **Tipo**: `A`
   - **Conteúdo**: `IP_DA_SUA_VPS`
   - **TTL**: `Auto` ou `300`
   - **Proxy**: ✅ Ativado (laranja) OU ⚪ Desativado (cinza)

3. Aguarde a propagação (pode levar de 5 minutos a 24 horas)

---

## ✅ Verificar se Funcionou

### **1. Testar DNS**
```bash
# No seu PC ou VPS
ping evolution-teste.aikanakamura.com
```

### **2. Testar HTTPS**
```bash
curl https://evolution-teste.aikanakamura.com
```

### **3. Verificar Certificado SSL**
- Acesse no navegador: `https://evolution-teste.aikanakamura.com`
- Clique no cadeado 🔒
- Verifique se o certificado é válido

### **4. Testar a API**
```bash
curl -X GET https://evolution-teste.aikanakamura.com \
  -H "apikey: 988aeb4c9250361977ddacd0fdb1ba1e"
```

### **5. Testar Botões**
```bash
curl -X POST https://evolution-teste.aikanakamura.com/message/sendButtons/Aika \
  -H "Content-Type: application/json" \
  -H "apikey: 988aeb4c9250361977ddacd0fdb1ba1e" \
  -d '{
    "number": "5519993369603",
    "title": "🎉 Novo Domínio!",
    "description": "evolution-teste.aikanakamura.com",
    "footer": "Testando correções",
    "buttons": [
      {
        "type": "reply",
        "displayText": "✅ Funcionou!",
        "id": "teste_ok"
      },
      {
        "type": "reply",
        "displayText": "🚀 Perfeito!",
        "id": "teste_perfeito"
      }
    ]
  }'
```

---

## 🐛 Troubleshooting

### **Problema: SSL/TLS não funciona**

**Solução 1 - Aguardar certificado**:
```bash
# Ver logs do Traefik
docker logs traefik -f

# Procure por mensagens sobre o certificado
```

**Solução 2 - Forçar renovação**:
- Deletar certificados antigos
- Reiniciar o Traefik
- Aguardar nova geração

### **Problema: Domínio não resolve**

**Verificar DNS**:
```bash
nslookup evolution-teste.aikanakamura.com
```

Se não resolver, verifique:
- ✅ DNS configurado corretamente
- ✅ Propagação concluída
- ✅ Firewall liberado (portas 80 e 443)

### **Problema: API não responde**

**Verificar containers**:
```bash
# Ver status
docker ps | grep evolution

# Ver logs
docker logs evolution_api -f

# Reiniciar
docker restart evolution_api
```

---

## 📚 Arquivos Importantes

- **`stack-portainer-evolution-teste.yaml`** - Stack com novo domínio
- **`docker-compose.production.yaml`** - Compose para Swarm (atualizado)
- **`ATUALIZAR_DOMINIO.md`** - Este guia

---

## ✨ Resumo das Mudanças

| Item | Antes | Depois |
|------|-------|--------|
| **Domínio** | evolution.aikanakamura.com | evolution-teste.aikanakamura.com |
| **SERVER_URL** | https://evolution.aikanakamura.com | https://evolution-teste.aikanakamura.com |
| **Router Traefik** | evolution | evolution-teste |
| **Service Traefik** | evolution | evolution-teste |

---

**Pronto!** 🎉 Seu domínio foi atualizado para **evolution-teste.aikanakamura.com**
