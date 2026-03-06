# 🤖 RAG com n8n + Supabase

## O que é RAG?

**RAG (Retrieval-Augmented Generation)** é uma técnica que combina busca em base de conhecimento com geração de texto por IA. Em vez de depender apenas do que o modelo aprendeu durante o treinamento, o RAG permite que a IA consulte documentos reais e atualizados antes de responder — tornando as respostas mais precisas, contextuais e confiáveis.

**Funcionamento resumido:**
1. Um documento é quebrado em pedaços (chunks) e transformado em vetores (embeddings)
2. Esses vetores são armazenados em um banco de dados vetorial
3. Quando o usuário faz uma pergunta, o sistema busca os chunks mais relevantes
4. A IA usa esses chunks como contexto para gerar a resposta final

---

## 📋 Sobre este fluxo

Workflow construído no **n8n** que implementa um agente de atendimento inteligente com base em documentos PDF, utilizando Supabase como banco vetorial e OpenAI para embeddings e geração de respostas.

O fluxo é dividido em duas partes:

---

### 1. Ingestão de Dados (Indexação)

```
Manual Trigger → Google Drive → Extract from PDF → Edit Fields → Supabase Vector Store
                                                                        ↑
                                              Embeddings OpenAI + Text Splitter
```

**Etapas:**
- **Manual Trigger** — inicia a execução manualmente
- **Google Drive** — baixa o arquivo PDF da base de conhecimento
- **Extract from PDF** — extrai o texto do documento
- **Edit Fields** — filtra apenas o campo `text`, descartando metadados técnicos do PDF
- **Recursive Character Text Splitter** — divide o texto em chunks com overlap configurável
- **Embeddings OpenAI** — transforma cada chunk em um vetor semântico
- **Supabase Vector Store** — armazena os vetores no banco de dados

---

### 2. Recuperação e Resposta (Retrieval)

```
Chat Trigger → AI Agent → Supabase Vector Store (busca)
                    ↑
             OpenAI GPT-4o-mini
```

**Etapas:**
- **Chat Trigger** — recebe a mensagem do usuário
- **AI Agent** — orquestra a busca e a geração da resposta
- **Query Data Tool** — busca os chunks mais relevantes no Supabase
- **OpenAI GPT-4o-mini** — gera a resposta final com base no contexto recuperado

---

## 🛠️ Tecnologias utilizadas

| Ferramenta | Uso |
|---|---|
| n8n | Orquestração do fluxo |
| OpenAI (GPT-4o-mini) | Modelo de linguagem para respostas |
| OpenAI Embeddings | Geração dos vetores semânticos |
| Supabase | Banco de dados vetorial (pgvector) |
| Google Drive | Armazenamento do documento fonte |

---

## ⚙️ Configuração

### Pré-requisitos
- Conta na [OpenAI](https://platform.openai.com) com chave de API
- Projeto no [Supabase](https://supabase.com) com extensão `pgvector` ativada e tabela vetorial criada
- Conta no Google Drive conectada ao n8n

### Credenciais necessárias
- `OpenAI API Key` — para embeddings e modelo de chat
- `Supabase URL + Key` — para conexão com o banco vetorial
- `Google Drive OAuth` — para download do PDF

### Parâmetros recomendados do Text Splitter
- **Chunk Size:** 500 caracteres
- **Chunk Overlap:** 50 caracteres

---

## 📁 Estrutura do repositório

```
├── RAG_in_n8n.json    # Workflow exportado do n8n
├── documento.pdf      # Base de conhecimento (substitua pelo seu)
└── README.md          # Este arquivo
```

---

## 🚀 Como usar

1. Importe o arquivo `RAG_in_n8n.json` no seu n8n
2. Configure as credenciais (OpenAI, Supabase, Google Cloud)
3. Faça upload do seu PDF no Google Drive e atualize o ID do arquivo no nó **Download file**
4. Execute o fluxo de ingestão manualmente para popular o banco vetorial
5. Acesse o chat e comece a interagir com o agente
