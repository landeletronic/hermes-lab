# OpenSpec — Stack Honcho self-hosted

## Implementação

- Honcho API, deriver, PostgreSQL/pgvector e Redis em Compose próprio.
- PostgreSQL e Redis exclusivos desta stack.
- LLM e embeddings apontam para o Ollama interno.
- Honcho usa `llama3.2:3b` no deriver, com `think=false` enviado como booleano em `provider_params.extra_body`.
- `structured_output_mode=json_object` permanece ativo para o transporte OpenAI-compatible do Ollama.
- Limites validados: `DERIVER_MAX_INPUT_TOKENS=1536` e `DERIVER_MODEL_CONFIG__MAX_OUTPUT_TOKENS=512`.
- `qwen3:4b` foi testado com `think=false` e descartado: truncou a representação em 96, 256 e 512 tokens.
- `llama3.2:3b` produziu duas observações persistidas em uma execução final; fila terminou vazia.
- Latência observada no processamento final: aproximadamente 172 segundos, com uma tentativa de retry de structured output.

## Critérios de aceitação

1. API e deriver iniciam com imagens fixadas.
2. Configuração do endpoint Ollama é aceita sem chave externa.
3. `/health` e MCP respondem somente internamente.
4. Deriver processa uma mensagem sintética.
5. Representação/conclusão é persistida no PostgreSQL exclusivo.
6. Hermes recupera contexto na sessão seguinte.
7. Redis, PostgreSQL e API não são publicados na LAN.
8. Falha do Ollama produz erro observável e não perda silenciosa.
