# OpenSpec — Stack Honcho self-hosted

## Implementação

- Honcho API, deriver, PostgreSQL/pgvector e Redis em Compose próprio.
- PostgreSQL e Redis exclusivos desta stack.
- LLM e embeddings apontam para o Ollama interno.
- Honcho usa `llama3.2:3b` inicialmente, com `qwen3:4b` como fallback de teste.
- Embeddings usam `nomic-embed-text:v1.5`, dimensão 768.

## Critérios de aceitação

1. API e deriver iniciam com imagens fixadas.
2. Configuração do endpoint Ollama é aceita sem chave externa.
3. `/health` e MCP respondem somente internamente.
4. Deriver processa uma mensagem sintética.
5. Representação/conclusão é persistida no PostgreSQL exclusivo.
6. Hermes recupera contexto na sessão seguinte.
7. Redis, PostgreSQL e API não são publicados na LAN.
8. Falha do Ollama produz erro observável e não perda silenciosa.
