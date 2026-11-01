# Execução autônoma — progresso 2026-11-01

## Concluído

- Pesquisa paralela de Hermes, Honcho, Mem0 e segurança.
- Docker Engine instalado e validado.
- Docker Compose V2 instalado e validado.
- Imagens Hermes, Honcho, PostgreSQL/pgvector e Redis puxadas por digest amd64.
- Diretórios persistentes criados em `/srv/hermes-lab`.
- Matriz de compatibilidade criada.
- Manifesto e especificações OpenSpec criados.
- Inventário público sanitizado para remover endereços e identificadores operacionais.
- Histórico de alterações versionado e publicado no GitHub.

## Estado atual

O Hermes continuará podendo usar Codex OAuth/ChatGPT para a interface principal, mas o processamento de memória será local para evitar credenciais adicionais:

```text
Hermes  → Codex OAuth/ChatGPT ou provider configurado no Hermes
Honcho  → Ollama
Mem0    → Ollama
```

A opção de usar `GPT-5.6-luna` deixa de ser a configuração principal do laboratório. O foco agora é validar `qwen3:4b` e `nomic-embed-text:v1.5` no host.
## Nova decisão

A estratégia foi alterada para usar uma LLM local com Ollama, evitando credenciais adicionais para Honcho e Mem0:

- provider: Ollama;
- LLM operacional inicial: `llama3.2:3b`;
- fallback para comparação: `qwen3:4b`;
- embeddings: `nomic-embed-text:v1.5`;
- um serviço Ollama compartilhado pelas duas stacks;
- modelos e volumes fora do checkout Git.

Os digests foram registrados. A compatibilidade do endpoint OpenAI-compatible do Ollama com Honcho e Mem0 ainda será testada antes do deploy final.

## Resultado preliminar de desempenho

O teste CPU-only no host mostrou aproximadamente:

- `llama3.2:3b`: resposta curta em 7 segundos;
- `qwen3:4b`: resposta curta em 46 segundos e saída de raciocínio extensa.

Por isso, o modelo operacional inicial foi alterado para `llama3.2:3b`, mantendo `qwen3:4b` instalado como fallback para comparar qualidade de extração e português. A decisão pode ser revertida somente após medir a carga real de Honcho/Mem0.

## Estado da validação de infraestrutura

- Firewall UFW ativo, entrada negada por padrão e SSH permitido somente pela LAN administrativa.
- Ollama está gerenciado por Compose, saudável, sem porta publicada e na rede Docker interna.
- Modelos baixados e identificados por digest:
  - `llama3.2:3b`;
  - `qwen3:4b`;
  - `nomic-embed-text:v1.5`.
- Embeddings validados com dimensão 768.
- Honcho PostgreSQL, Redis e API foram iniciados com sucesso.
- A API Honcho respondeu `/health` com `{"status":"ok"}`.
- A imagem Honcho foi reconstruída com os tokenizers necessários pré-carregados, evitando dependência de DNS externo em runtime.
- O teste de derivação Honcho iniciou chamadas ao Ollama, mas apresentou latência excessiva em CPU e permaneceu com work units em andamento. O deriver foi interrompido para proteger os recursos do host.
- Nenhum Webapp Hermes foi iniciado.
- Nenhum serviço foi publicado na LAN.

## Validação Mem0 OSS

- SDK Mem0 OSS `2.2.1` executado com Ollama e PostgreSQL/pgvector isolado.
- Configuração corrigida para `embedding_model_dims=768`; sem essa configuração o SDK criava a tabela com o padrão OpenAI de 1536 dimensões.
- `add(infer=true)`: aprovado com persistência de memória sintética.
- `search(filters={user_id})`: aprovado, com resultado semântico retornado.
- `update`: aprovado.
- `delete`: aprovado; busca posterior retornou lista vazia.
- O plugin Mem0 foi instalado e o Hermes reportou `Provider: mem0`, `Plugin: installed` e `Status: available` em uma imagem local de teste.
- A integração automática Hermes + Mem0 ainda não foi aceita como ponta a ponta; falta executar uma sessão Hermes com provider ativo e modelo principal autenticado.

## Ajuste de desempenho

- `OLLAMA_MAX_LOADED_MODELS` alterado para `2`.
- `OLLAMA_KEEP_ALIVE` alterado para `10m`, mantendo LLM e embeddings aquecidos durante o workload.
- Mem0 com `llama3.2:3b`: aproximadamente 360 segundos para uma extração curta.
- Mem0 com `llama3.2:1b`: aproximadamente 119 segundos; não adequado para o deriver Honcho porque falhou na saída JSON estruturada.
- Honcho com `llama3.2:1b`: fila processada, mas falhou na validação de saída estruturada.
- Honcho permanece com `llama3.2:3b` para nova validação; ainda não há conclusão derivada verificável.

## Bloqueios atuais

1. O modo HTTP self-hosted do Mem0, no commit inspecionado, não aceita Ollama entre os providers bundled. O caminho adotado é Mem0 OSS in-process com Ollama e pgvector.
2. A integração automática Mem0 OSS com uma sessão Hermes ainda precisa de teste ponta a ponta.
3. Honcho ainda não produziu uma conclusão sintética verificável com o modelo local no host; o Webapp não deve ser iniciado antes desse gate.
4. O plugin Honcho do catálogo falhou na resolução de dependências do Hermes v0.22.5 por erro de workspace/uv; a instalação manual do SDK também exige empacotamento separado do plugin.
5. O login OAuth/Codex do Hermes ainda não foi executado; não iniciar Webapps autenticados sem esse gate.
