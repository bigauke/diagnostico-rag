# Lab 4: Diagnóstico de Falhas RAG

Este repositório contém o notebook referente ao laboratório de **Diagnóstico de Falhas em Pipelines RAG** (Retrieval-Augmented Generation). 

## Objetivo

O principal objetivo deste notebook é demonstrar, de forma prática, como identificar e corrigir as falhas mais comuns em sistemas RAG usando métricas quantitativas do framework **RAGAS** (como `faithfulness` e `context_precision`).

---

## Cenários Avaliados

O laboratório simula três situações problemáticas comuns no ciclo de vida do RAG, faz um diagnóstico usando RAGAS e implementa a solução (Fix):

### Cenário A: Fragmentação de Conceitos (Problema de Chunking)
- **A Falha:** Uso de chunks muito curtos (200 caracteres) e sem sobreposição (`overlap = 0`). Isso "corta" o raciocínio dos documentos originais.
- **Sintoma (RAGAS):** Baixo `faithfulness` (Fidelidade), pois o modelo recebe trechos desconexos e perde o sentido real.
- **O Fix:** Aumentar o `chunk_size` para 800 e aplicar um `chunk_overlap` de 100, garantindo que o contexto da informação seja preservado.

### Cenário B: Incompatibilidade de Vocabulário (Retrieval Mismatch)
- **A Falha:** O usuário faz uma pergunta usando linguagem coloquial (ex: *"como o computador aprende sozinho"*), mas a base de dados só contém jargões técnicos (ex: *"unsupervised learning"*). 
- **Sintoma (RAGAS):** Baixa `context_precision`. Os embeddings da pergunta e do texto não "batem", trazendo lixo nos Top-K resultados.
- **O Fix:** Aplicar *Query Rewrite* (reescrita de query) para traduzir a pergunta do usuário para termos técnicos antes de passá-la para a busca vetorial.

### Cenário C: Alucinação por Prompt Sem Âncora
- **A Falha:** O prompt passado ao LLM é muito permissivo. Quando o usuário pergunta algo que não existe na base (ex: GPT-5), o LLM ignora o contexto e responde usando seu conhecimento interno.
- **Sintoma (RAGAS):** O LLM gera uma "resposta inventada" (alucinação perante o contexto), prejudicando a confiabilidade do RAG.
- **O Fix:** Utilizar um **Prompt Estrito com Fallback**: *"Responda APENAS com base no contexto. Se não encontrar, diga 'Não encontrado'."*

---

## Tecnologias e Bibliotecas Utilizadas

- **ChromaDB**: Banco de dados vetorial local para indexar e recuperar os chunks.
- **RAGAS**: Framework open-source focado em avaliação de RAG.
- **LangChain / SentenceTransformers**: Para a camada de embeddings (ex: modelo `all-MiniLM-L6-v2`) e ingestão (`RecursiveCharacterTextSplitter`).
- **Groq API**: Provedor de LLM para orquestração e julgamento das respostas (`llama-3.1-8b-instant`).

## Como utilizar

Para reproduzir este laboratório localmente:

1. Clone o repositório.
2. Certifique-se de configurar um arquivo `.env` na raiz contendo sua chave da Groq: `GROQ_API_KEY=sua_chave_aqui`.
3. Garanta que você possui os PDFs de teste na pasta `data/corpus/`.
4. Execute as células do Jupyter Notebook sequencialmente. Ao final da execução, ele irá gerar um relatório (`04-diagnostico-table.csv`) com o quadro de Antes e Depois (Falhas vs Soluções).
