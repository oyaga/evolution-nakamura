# 🤖 Guia N8N - Testar Botões da Evolution API

## 📦 Arquivos Disponíveis

1. **`n8n-teste-botoes.json`** - Workflow simples para teste rápido
2. **`n8n-exemplos-botoes.json`** - Workflow completo com 5 exemplos diferentes

---

## 🚀 Como Importar no N8N

### **Passo 1: Acessar o N8N**
- Abra seu N8N: `http://seu-n8n.com` ou `http://localhost:5678`

### **Passo 2: Importar Workflow**

1. No N8N, clique no menu superior direito (☰)
2. Clique em **"Import from File"** ou **"Workflows"** → **"Import"**
3. Selecione um dos arquivos:
   - `n8n-teste-botoes.json` (teste simples)
   - `n8n-exemplos-botoes.json` (exemplos completos)
4. Clique em **"Import"**

### **Passo 3: Configurar (se necessário)**

Se precisar alterar configurações, edite os nodes:

**Campos principais:**
- `url`: `https://evolution-teste.aikanakamura.com/message/sendButtons/Aika`
- `apikey`: `988aeb4c9250361977ddacd0fdb1ba1e`
- `number`: `5519993369603` (seu número de teste)

---

## 🎯 Workflow 1: Teste Simples

**Arquivo**: `n8n-teste-botoes.json`

### O que faz:
- Envia uma mensagem com 3 botões de resposta (reply)
- Ideal para teste rápido

### Como usar:
1. Importe o workflow
2. Clique em **"Test workflow"** (ou pressione `Ctrl+Enter`)
3. Verifique seu WhatsApp! 📱

### Resposta esperada:
```json
{
  "key": {
    "remoteJid": "5519993369603@s.whatsapp.net",
    "fromMe": true,
    "id": "..."
  },
  "message": {
    "viewOnceMessage": {
      "message": {
        "interactiveMessage": {...}
      }
    }
  }
}
```

---

## 📚 Workflow 2: Exemplos Completos

**Arquivo**: `n8n-exemplos-botoes.json`

### Contém 5 tipos de botões:

#### 1️⃣ **Botões de Resposta (Reply)**
```json
{
  "buttons": [
    {
      "type": "reply",
      "displayText": "Sim",
      "id": "resposta_sim"
    },
    {
      "type": "reply",
      "displayText": "Não",
      "id": "resposta_nao"
    }
  ]
}
```
- **Uso**: Menu de opções, confirmações, escolhas
- **Limite**: Máximo 3 botões

#### 2️⃣ **Botão de URL**
```json
{
  "buttons": [
    {
      "type": "url",
      "displayText": "Visitar Site",
      "url": "https://aikanakamura.com"
    }
  ]
}
```
- **Uso**: Direcionar para sites, landing pages
- **Limite**: 1 botão

#### 3️⃣ **Botão de Chamada (Call)**
```json
{
  "buttons": [
    {
      "type": "call",
      "displayText": "Ligar Agora",
      "phoneNumber": "+5519993369603"
    }
  ]
}
```
- **Uso**: Atendimento, suporte telefônico
- **Limite**: 1 botão

#### 4️⃣ **Botão de Copiar (Copy)**
```json
{
  "buttons": [
    {
      "type": "copy",
      "displayText": "Copiar Código",
      "copyCode": "DESCONTO50"
    }
  ]
}
```
- **Uso**: Cupons, códigos promocionais, chaves
- **Limite**: 1 botão

#### 5️⃣ **Botões com Imagem**
```json
{
  "thumbnailUrl": "https://evolution-api.com/files/logo.png",
  "title": "Título",
  "description": "Descrição",
  "buttons": [...]
}
```
- **Uso**: Mensagens mais atrativas com visual
- **Formato**: URL pública da imagem

### Como usar:
1. Importe o workflow
2. **Execute cada node separadamente** (clique no node → "Execute Node")
3. Verifique no WhatsApp cada tipo de botão

---

## 🔧 Personalizar os Workflows

### Alterar o Número de Destino

Edite o node HTTP Request → Body JSON:
```json
{
  "number": "SEU_NUMERO_AQUI",
  ...
}
```

### Alterar a Instância

Na URL do node:
```
https://evolution-teste.aikanakamura.com/message/sendButtons/SUA_INSTANCIA
```

### Alterar a API Key

Nos Headers do node:
```json
{
  "name": "apikey",
  "value": "SUA_API_KEY_AQUI"
}
```

### Alterar Botões

Edite o `jsonBody`:
```json
{
  "buttons": [
    {
      "type": "reply",
      "displayText": "Novo Texto",
      "id": "novo_id"
    }
  ]
}
```

---

## 📋 Estrutura Completa da Requisição

```json
{
  "number": "5519993369603",
  "thumbnailUrl": "https://url-da-imagem.jpg",
  "title": "Título da Mensagem",
  "description": "Descrição/Corpo da mensagem",
  "footer": "Texto do rodapé",
  "buttons": [
    {
      "type": "reply | url | call | copy | pix",
      "displayText": "Texto do botão",
      "id": "id_unico",
      "url": "https://...",
      "phoneNumber": "+55...",
      "copyCode": "CODIGO123"
    }
  ]
}
```

### Campos Obrigatórios:
- ✅ `number` - Número do destinatário
- ✅ `title` - Título da mensagem
- ✅ `buttons` - Array com pelo menos 1 botão
- ✅ `buttons[].type` - Tipo do botão
- ✅ `buttons[].displayText` - Texto do botão

### Campos Opcionais:
- ⚪ `thumbnailUrl` - URL da imagem (thumbnail)
- ⚪ `description` - Descrição/corpo da mensagem
- ⚪ `footer` - Texto do rodapé

---

## 🎨 Exemplos de Uso Prático

### **Caso 1: Menu de Atendimento**
```json
{
  "number": "5519993369603",
  "title": "🏢 Menu de Atendimento",
  "description": "Como podemos ajudar você hoje?",
  "footer": "Escolha uma opção",
  "buttons": [
    {"type": "reply", "displayText": "💰 Vendas", "id": "vendas"},
    {"type": "reply", "displayText": "🛠️ Suporte", "id": "suporte"},
    {"type": "reply", "displayText": "📞 Falar com Humano", "id": "humano"}
  ]
}
```

### **Caso 2: Confirmação de Pedido**
```json
{
  "number": "5519993369603",
  "title": "✅ Confirmar Pedido #12345",
  "description": "Pedido: 3x Pizza Grande\nTotal: R$ 89,90\n\nConfirmar este pedido?",
  "footer": "Pizza Express",
  "buttons": [
    {"type": "reply", "displayText": "✅ Confirmar", "id": "confirmar_pedido"},
    {"type": "reply", "displayText": "❌ Cancelar", "id": "cancelar_pedido"}
  ]
}
```

### **Caso 3: Cupom de Desconto**
```json
{
  "number": "5519993369603",
  "title": "🎁 Cupom Especial para Você!",
  "description": "Use o código abaixo e ganhe 50% OFF na primeira compra!",
  "footer": "Válido até 31/12/2024",
  "buttons": [
    {"type": "copy", "displayText": "📋 Copiar Cupom", "copyCode": "PRIMEIRACOMPRA50"}
  ]
}
```

### **Caso 4: Landing Page**
```json
{
  "number": "5519993369603",
  "thumbnailUrl": "https://exemplo.com/produto.jpg",
  "title": "🚀 Novo Produto Lançado!",
  "description": "Confira nosso novo produto e aproveite o desconto de lançamento",
  "footer": "Loja Online",
  "buttons": [
    {"type": "url", "displayText": "🛒 Ver Produto", "url": "https://loja.com/produto"}
  ]
}
```

---

## ✅ Checklist de Testes

Após importar, teste cada tipo:

- [ ] Botões de resposta (reply) - 3 botões
- [ ] Botão de URL - abre link
- [ ] Botão de chamada (call) - inicia ligação
- [ ] Botão de copiar (copy) - copia texto
- [ ] Botões com imagem - mostra thumbnail

---

## 🐛 Troubleshooting

### **Erro: "Authentication failed"**
- Verifique a API Key nos headers
- Certifique-se de que está usando a key correta

### **Erro: "Instance not found"**
- Verifique o nome da instância na URL
- Certifique-se de que a instância existe e está conectada

### **Botões não aparecem no WhatsApp**
- ✅ Verifique se usou a imagem customizada (`evolution-api-custom:latest`)
- ✅ As correções estão aplicadas apenas na versão custom
- ✅ Veja os logs: `docker logs evolution_api -f`

### **Erro: "Number is not on WhatsApp"**
- Verifique se o número está correto
- Use apenas números sem caracteres especiais
- Formato: `5519993369603` (sem + ou espaços)

---

## 📊 Monitorar Execuções

### No N8N:
1. Vá em **"Executions"** no menu lateral
2. Veja o histórico de execuções
3. Clique em uma execução para ver detalhes

### Nos Logs da Evolution:
```bash
# SSH na VPS
docker logs evolution_api -f

# Procure por:
# "Sending message to 5519993369603@s.whatsapp.net"
```

---

## 🎉 Pronto!

Agora você tem workflows prontos para testar todos os tipos de botões da Evolution API!

**Arquivos criados:**
- ✅ `n8n-teste-botoes.json` - Teste rápido
- ✅ `n8n-exemplos-botoes.json` - Exemplos completos
- ✅ `GUIA_N8N_BOTOES.md` - Este guia

**Próximos passos:**
1. Importar workflows no N8N
2. Executar os testes
3. Verificar botões no WhatsApp
4. Integrar nos seus fluxos de automação

---

**Dúvidas?** Consulte:
- [Documentação Evolution API](https://doc.evolution-api.com/)
- [Documentação N8N](https://docs.n8n.io/)
- Logs: `docker logs evolution_api -f`
