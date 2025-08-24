# Guia Completo n8n: Do Básico ao Avançado

🎥 **Canal no YouTube:** [youtube.com/@jonathandacruz](https://www.youtube.com/@jonathandacruz)

## 📋 Índice
1. [Introdução ao n8n](#introdução)
2. [Configuração e Instalação](#instalação)
3. [Conceitos Fundamentais](#conceitos)
4. [Primeiros Workflows](#primeiros-workflows)
5. [Nós Essenciais](#nós-essenciais)
6. [Manipulação de Dados](#manipulação-dados)
7. [Autenticação e APIs](#autenticação)
8. [Workflows Condicionais](#condicionais)
9. [Loops e Iterações](#loops)
10. [Tratamento de Erros](#erros)
11. [Webhooks e Triggers](#webhooks)
12. [Integração com Bancos de Dados](#bancos)
13. [Automação de E-mail](#email)
14. [Integração com Redes Sociais](#redes-sociais)
15. [Funções JavaScript](#javascript)
16. [Subworkflows](#subworkflows)
17. [Monitoramento e Logs](#monitoramento)
18. [Performance e Otimização](#performance)
19. [Segurança e Boas Práticas](#segurança)
20. [Casos de Uso Avançados](#casos-avançados)

---

## 1. Introdução ao n8n {#introdução}

### O que é o n8n?
- **Ferramenta de automação open-source** para conectar diferentes serviços
- **Interface visual** para criar workflows sem código
- **Extensível** com JavaScript personalizado
- **Self-hosted** ou cloud (n8n.cloud)

### Vantagens
- ✅ Gratuito e open-source
- ✅ Interface intuitiva
- ✅ 400+ integrações nativas
- ✅ Suporte a código personalizado
- ✅ Execução local ou na nuvem

---

## 2. Configuração e Instalação {#instalação}

### Opção 1: Docker (Recomendado)
```bash
# Docker Compose básico
version: '3.1'
services:
  n8n:
    image: n8nio/n8n:latest
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=password
    volumes:
      - n8n_data:/home/node/.n8n
volumes:
  n8n_data:
```

### Opção 2: npm
```bash
npm install n8n -g
n8n start
```

### Opção 3: n8n Self Hosted
- Assista o vídeo de como hospedar seu próprio servidor de n8n
🎥 **n8n Self Hosted:** [[youtube.com/@jonathandacruz](https://youtu.be/GsRs2B99ZoA)] 

### Configurações Iniciais
- **URL Base**: http://localhost:5678
- **Credenciais**: Configure no primeiro acesso
- **Timezone**: Ajuste para seu fuso horário
- **Webhook URL**: Configure para triggers externos

---

## 3. Conceitos Fundamentais {#conceitos}

### Workflow (Fluxo de Trabalho)
- **Definição**: Sequência de nós conectados que executam tarefas
- **Execução**: Manual ou automática (triggers)
- **Dados**: Fluem de um nó para outro

### Nós (Nodes)
- **Trigger Nodes**: Iniciam o workflow
- **Regular Nodes**: Executam ações específicas
- **Credentials**: Armazenam informações de autenticação

### Conexões
- **Linhas**: Conectam nós e transferem dados
- **Main**: Conexão principal (dados de sucesso)
- **Error**: Conexão de erro (falhas)

### Execuções
- **Manual**: Clique em "Execute Workflow"
- **Automática**: Via triggers (webhook, cron, etc.)
- **Histórico**: Todas as execuções são registradas

---

## 4. Primeiros Workflows {#primeiros-workflows}

### Workflow 1: Hello World
```
[Manual Trigger] → [Set] → [HTTP Request]
```

**Configuração Set Node:**
```json
{
  "message": "Hello World!",
  "timestamp": "{{ $now }}"
}
```

### Workflow 2: Monitoramento de Site
```
[Cron Trigger] → [HTTP Request] → [IF] → [Gmail]
```

**Configuração Cron:**
- Expressão: `0 */5 * * * *` (a cada 5 minutos)

**Configuração IF:**
- Condição: `{{ $json.status }} !== 200`

### Workflow 3: Backup Automático
```
[Schedule Trigger] → [Postgres] → [Set] → [Google Drive]
```

---

## 5. Nós Essenciais {#nós-essenciais}

### Trigger Nodes
- **Manual Trigger**: Execução manual
- **Webhook**: Recebe dados via HTTP
- **Cron**: Agendamento por tempo
- **File Trigger**: Monitora arquivos
- **Email Trigger**: Monitora emails

### Core Nodes
- **Set**: Manipula dados
- **Code**: JavaScript personalizado
- **IF**: Lógica condicional
- **Switch**: Múltiplas condições
- **Merge**: Combina dados
- **Split**: Divide arrays

### Utility Nodes
- **Wait**: Pausa execução
- **Stop and Error**: Para execução
- **No Op**: Node vazio (debugging)
- **Sticky Note**: Comentários visuais

---

## 6. Manipulação de Dados {#manipulação-dados}

### Expressões Básicas
```javascript
// Data atual
{{ $now }}

// Formato de data
{{ $now.format('YYYY-MM-DD') }}

// Dados do nó anterior
{{ $json.campo }}

// Array de dados
{{ $json.items[0].name }}

// Contar items
{{ $json.items.length }}
```

### Set Node - Exemplos
```json
{
  // Texto simples
  "nome": "João",
  
  // Expressão
  "data_processamento": "{{ $now }}",
  
  // Manipulação de string
  "nome_uppercase": "{{ $json.nome.toUpperCase() }}",
  
  // Cálculo
  "preco_com_desconto": "{{ $json.preco * 0.9 }}",
  
  // Array
  "tags": ["automacao", "n8n", "workflow"]
}
```

### Code Node - JavaScript
```javascript
// Acesso aos dados
const items = $input.all();

// Processamento
const processedItems = items.map(item => {
  return {
    ...item.json,
    processed: true,
    timestamp: new Date().toISOString()
  };
});

return processedItems;
```

---

## 7. Autenticação e APIs {#autenticação}

### Tipos de Credenciais
- **API Key**: Chave simples
- **OAuth2**: Fluxo completo
- **Basic Auth**: Usuário/senha
- **Bearer Token**: Token JWT

### Exemplo HTTP Request com API
```json
{
  "method": "GET",
  "url": "https://api.github.com/user/repos",
  "headers": {
    "Authorization": "token {{ $credentials.github.token }}",
    "User-Agent": "n8n-workflow"
  }
}
```

### Gerenciamento de Credenciais
- **Reutilização**: Uma credencial para múltiplos workflows
- **Segurança**: Dados criptografados
- **Teste**: Validação automática

---

## 8. Workflows Condicionais {#condicionais}

### IF Node
```javascript
// Condição simples
{{ $json.idade >= 18 }}

// Múltiplas condições
{{ $json.status === 'ativo' && $json.score > 80 }}

// Verificar existência
{{ $json.email !== undefined }}
```

### Switch Node
```javascript
// Configuração de rotas
{
  "rules": [
    {
      "condition": "{{ $json.tipo === 'premium' }}",
      "output": 0
    },
    {
      "condition": "{{ $json.tipo === 'basico' }}",
      "output": 1
    }
  ]
}
```

---

## 9. Loops e Iterações {#loops}

### SplitInBatches Node
```json
{
  "batchSize": 10,
  "options": {
    "reset": false
  }
}
```

### Code Node - Loop Manual
```javascript
const items = $input.all();
const results = [];

for (const item of items) {
  // Processa cada item
  const processed = {
    original: item.json,
    processed_at: new Date(),
    status: 'completed'
  };
  results.push(processed);
}

return results;
```

---

## 10. Tratamento de Erros {#erros}

### Try-Catch com Continue on Fail
- Ative "Continue on Fail" nos nós
- Use IF para verificar erros
- Implemente ações alternativas

### Error Workflow
```
[Any Node] --error--> [Set] → [Slack] → [Stop]
```

### Code Node - Try/Catch
```javascript
try {
  const result = await $http.get('https://api.example.com/data');
  return [{ json: result.data }];
} catch (error) {
  return [{
    json: {
      error: true,
      message: error.message,
      timestamp: new Date()
    }
  }];
}
```

---

## 11. Webhooks e Triggers {#webhooks}

### Webhook Node
```json
{
  "httpMethod": "POST",
  "path": "meu-webhook",
  "responseMode": "responseNode"
}
```

### Webhook Response
```json
{
  "status": 200,
  "body": {
    "success": true,
    "message": "Dados recebidos com sucesso",
    "timestamp": "{{ $now }}"
  }
}
```

### Exemplo Completo - API Simples
```
[Webhook] → [Switch] → [Set] → [Webhook Response]
```

---

## 12. Integração com Bancos de Dados {#bancos}

### PostgreSQL Node
```sql
-- SELECT
SELECT id, nome, email FROM usuarios WHERE ativo = true

-- INSERT
INSERT INTO logs (evento, timestamp) 
VALUES ('{{ $json.evento }}', '{{ $now }}')

-- UPDATE
UPDATE produtos SET estoque = estoque - {{ $json.quantidade }} 
WHERE id = {{ $json.produto_id }}
```

### MySQL Node
```sql
-- Procedure
CALL processar_pedido({{ $json.pedido_id }})

-- JOIN
SELECT p.nome, c.nome as categoria 
FROM produtos p 
JOIN categorias c ON p.categoria_id = c.id
```

---

## 13. Automação de E-mail {#email}

### Gmail Node - Envio
```json
{
  "to": "{{ $json.email }}",
  "subject": "Bem-vindo {{ $json.nome }}!",
  "message": "Olá {{ $json.nome }},\n\nSeu cadastro foi realizado com sucesso.",
  "attachments": [
    {
      "name": "manual.pdf",
      "data": "{{ $json.arquivo_base64 }}"
    }
  ]
}
```

### Email Trigger - Monitoramento
```json
{
  "format": "simple",
  "mailbox": "INBOX",
  "markAsRead": true,
  "downloadAttachments": true
}
```

---

## 14. Integração com Redes Sociais {#redes-sociais}

### Twitter/X Node
```json
{
  "operation": "tweet",
  "text": "Novo produto lançado! {{ $json.produto_nome }} #lancamento"
}
```

### LinkedIn Node
```json
{
  "operation": "post",
  "text": "{{ $json.conteudo }}",
  "visibility": "public"
}
```

### Slack Node
```json
{
  "channel": "#geral",
  "text": "🚨 Alerta: {{ $json.mensagem }}",
  "attachments": [
    {
      "color": "danger",
      "fields": [
        {
          "title": "Servidor",
          "value": "{{ $json.servidor }}",
          "short": true
        }
      ]
    }
  ]
}
```

---

## 15. Funções JavaScript {#javascript}

### Funções de Data
```javascript
// Formatação
$now.format('DD/MM/YYYY HH:mm')

// Manipulação
$now.plus({ days: 7 })
$now.minus({ hours: 2 })

// Comparação
$now.diff($json.data_criacao, 'days')
```

### Funções de String
```javascript
// Manipulação básica
$json.texto.toUpperCase()
$json.texto.toLowerCase()
$json.texto.trim()

// Busca e substituição
$json.texto.replace('antigo', 'novo')
$json.texto.includes('palavra')
$json.texto.split(',')

// Regex
$json.email.match(/^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$/)
```

### Funções de Array
```javascript
// Manipulação
$json.items.map(item => item.name)
$json.items.filter(item => item.active)
$json.items.find(item => item.id === 1)

// Agregação
$json.valores.reduce((sum, val) => sum + val, 0)
$json.items.length
```

---

## 16. Subworkflows {#subworkflows}

### Execute Workflow Node
```json
{
  "workflowId": "123",
  "waitForCompletion": true,
  "data": {
    "usuario_id": "{{ $json.id }}",
    "acao": "processar"
  }
}
```

### Estrutura Modular
```
Main Workflow:
[Trigger] → [Validate] → [Execute Workflow: Process] → [Execute Workflow: Notify]

Process Workflow:
[Start] → [Database] → [Transform] → [End]

Notify Workflow:
[Start] → [Email] → [Slack] → [End]
```

---

## 17. Monitoramento e Logs {#monitoramento}

### Executions Log
- **Visualização**: Histórico completo
- **Filtros**: Por status, data, workflow
- **Debug**: Dados de cada nó

### Custom Logging
```javascript
// Code Node - Log personalizado
console.log('Processando item:', $json.id);

return [{
  json: {
    ...$json,
    log_message: `Item ${$json.id} processado em ${new Date()}`
  }
}];
```

### Métricas Importantes
- **Taxa de sucesso**: % execuções bem-sucedidas
- **Tempo médio**: Duração das execuções
- **Erros frequentes**: Padrões de falha

---

## 18. Performance e Otimização {#performance}

### Boas Práticas
- **Batch Processing**: Processe em lotes
- **Indexação**: Use índices em bancos
- **Cache**: Reutilize dados quando possível
- **Paralelização**: Execute nós em paralelo

### Exemplo Otimizado
```javascript
// Code Node - Processamento eficiente
const items = $input.all();
const BATCH_SIZE = 100;
const results = [];

for (let i = 0; i < items.length; i += BATCH_SIZE) {
  const batch = items.slice(i, i + BATCH_SIZE);
  const processed = await processBatch(batch);
  results.push(...processed);
}

return results;
```

---

## 19. Segurança e Boas Práticas {#segurança}

### Segurança
- **Credenciais**: Nunca hardcode senhas
- **HTTPS**: Use conexões seguras
- **Validação**: Valide dados de entrada
- **Logs**: Não registre dados sensíveis

### Estrutura de Projeto
```
workflows/
├── production/
│   ├── user-registration.json
│   └── order-processing.json
├── staging/
│   └── test-workflows.json
└── development/
    └── experimental.json
```

### Documentação
- **Sticky Notes**: Comente workflows
- **Naming**: Use nomes descritivos
- **Versionamento**: Mantenha histórico

---

## 20. Casos de Uso Avançados {#casos-avançados}

### 1. Sistema de Aprovação
```
[Webhook] → [Database Check] → [IF: Needs Approval] 
    ├─ Yes → [Slack Notification] → [Wait for Webhook] → [Process]
    └─ No → [Process] → [Notify Result]
```

### 2. ETL Pipeline
```
[Schedule] → [Extract: API] → [Transform: Code] → [Load: Database] → [Notify: Email]
```

### 3. Monitoramento de Infraestrutura
```
[Cron: Every 5min] → [HTTP: Health Check] → [IF: Down]
    ├─ Yes → [PagerDuty] → [Slack] → [Teams Notification]
    └─ No → [Log: OK]
```

### 4. E-commerce Automation
```
[Webhook: New Order] → [Inventory Check] → [IF: In Stock]
    ├─ Yes → [Payment Process] → [Send Confirmation] → [Update Inventory]
    └─ No → [Backorder Process] → [Notify Customer]
```

### 5. Lead Scoring
```
[CRM Webhook] → [Enrich Data: API] → [Score Calculation] → [IF: Hot Lead]
    ├─ Yes → [Assign Sales Rep] → [Send Alert]
    └─ No → [Add to Nurture Campaign]
```

---

## 🎯 Próximos Passos

### Iniciante
1. Configure sua instância n8n
2. Crie seu primeiro workflow manual
3. Experimente com diferentes nós
4. Pratique manipulação de dados

### Intermediário
1. Implemente triggers automáticos
2. Integre com APIs externas
3. Use condicionais e loops
4. Configure tratamento de erros

### Avançado
1. Desenvolva subworkflows
2. Implemente monitoramento
3. Otimize performance
4. Crie soluções empresariais

---

## 📚 Recursos Adicionais

- **Documentação Oficial**: https://docs.n8n.io
- **Community**: [https://jonathandacruz.com.br/automacao-n8n](https://jonathandacruz.com.br/automacao-n8n)
- **Templates**: [https://jonathandacruz.com.br/templates](https://jonathandacruz.com.br/templates)
- **GitHub**: https://github.com/n8n-io/n8n

---

*Este guia cobre todos os aspectos essenciais do n8n. Pratique cada seção e adapte os exemplos às suas necessidades específicas!*
