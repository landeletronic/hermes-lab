# OpenSpec — Ollama local provider

## Objetivo

Fornecer LLM e embeddings locais para Honcho e Mem0 sem credenciais externas.

## Configuração inicial

- Imagem: `ollama/ollama@sha256:b2f0122c476bc9fe7ce679d8bb4bd555351e25e2554eefb156f61cb0a89cf429`.
- Versão observada: `0.40.13`.
- Modelo generativo operacional: `llama3.2:3b` — digest `a80c4f17acd55265feec403c7aef86be0c25983ab279d83f3bcd3abbcb5b8b72`.
- Modelo generativo fallback: `qwen3:4b` — digest `359d7dd4bcdab3d86b87d73ac27966f4dbb9f5efdfcc75d34a8764a09474fae7`.
- Embeddings: `nomic-embed-text:v1.5` — digest `0a109f422b47e3a30ba2b10eca18548e944e8a23073ee3f3e947efcf3c45e59f`.
- Dimensão dos embeddings: 768.
- Rede: interna Docker, sem porta publicada.
- Limite inicial: 4 GiB RAM, 2 CPUs, 512 PIDs.
- Concorrência: 1.
- `OLLAMA_MAX_LOADED_MODELS=1`.
- `OLLAMA_NUM_PARALLEL=1`.
- `OLLAMA_KEEP_ALIVE=0`.

## Critérios de aceitação

1. Container saudável e acessível somente pelas redes internas autorizadas.
2. `/api/tags` lista os dois modelos.
3. `/api/chat` retorna resposta com `qwen3:4b`.
4. `/api/embed` retorna vetor com dimensão registrada.
5. Endpoint OpenAI-compatible é testado separadamente para chat e embeddings.
6. Structured output é validado antes de configurar Honcho/Mem0.
7. RSS, swap, latência e tempo de carregamento são medidos.
8. Nenhuma porta Ollama fica publicada no host.

## Riscos e rollback

- Se houver OOM ou latência impraticável, descarregar o modelo e testar `llama3.2:3b`.
- Se a compatibilidade OpenAI-compatible falhar, testar configuração nativa específica do consumidor.
- Remoção: parar/remover o container sem apagar `/srv/hermes-lab/ollama` até backup validado.
