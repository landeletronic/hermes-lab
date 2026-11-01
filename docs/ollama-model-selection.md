# Seleção de LLM local com Ollama

## Inventário relevante

O host possui:

- 4 vCPUs;
- aproximadamente 7,2 GiB de RAM;
- 8 GiB de swap;
- armazenamento suficiente;
- sem GPU identificada no inventário.

A execução será considerada CPU-only até que uma GPU seja confirmada.

## Recomendação inicial

Usar um único serviço Ollama compartilhado pelas duas stacks, evitando carregar duas cópias do modelo na memória:

```text
Ollama
├── Qwen3 4B — LLM de extração/derivação
└── nomic-embed-text:v1.5 — embeddings
```

### LLM: Qwen3 4B

Pin lógico inicial:

```text
qwen3:4b
```

Motivos:

- tamanho aproximado de 2,5 GB no catálogo Ollama;
- suporte multilíngue, incluindo português;
- capacidade de seguir instruções e usar ferramentas;
- adequado para extração e síntese de memória em um host pequeno;
- melhor candidato inicial que modelos de 7B/8B para a RAM disponível.

O digest final deve ser obtido depois do `ollama pull` e registrado no manifesto. A tag sozinha não é considerada suficiente para deployment reproduzível.

### Embeddings: nomic-embed-text

Pin lógico inicial:

```text
nomic-embed-text:v1.5
```

Motivos:

- modelo dedicado a embeddings;
- tamanho aproximado de 274 MB;
- adequado para busca semântica;
- reduz o custo de memória em comparação com usar a LLM para embeddings.

A dimensão efetiva deve ser confirmada no runtime e configurada no pgvector. Não iniciar a coleção vetorial antes dessa validação.

## Arquitetura de memória

```text
Hermes Honcho ─┐
               ├── rede interna → Ollama → Qwen3 4B / nomic-embed-text
Hermes Mem0 ───┘
```

O Ollama não será publicado na LAN. Apenas os containers do laboratório poderão acessá-lo.

## Limites recomendados

- Um único modelo generativo carregado por vez.
- `OLLAMA_MAX_LOADED_MODELS=1`.
- Limite de memória do container Ollama a ser definido após medir o uso real.
- Sem GPU até confirmação explícita.
- Modelos persistidos fora do checkout Git.
- Health check da API Ollama antes de iniciar Honcho/Mem0.

## Compatibilidade a validar

1. Honcho usando endpoint OpenAI-compatible do Ollama.
2. Mem0 self-hosted usando `OPENAI_BASE_URL` apontando para Ollama.
3. Suporte do Qwen3 4B para extração estruturada exigida pelos serviços.
4. Dimensão e qualidade do `nomic-embed-text:v1.5` no pgvector.
5. Consumo simultâneo com Hermes, Honcho, Mem0, PostgreSQL e Redis.
6. Latência aceitável em CPU.

## Alternativa de fallback

Se Qwen3 4B exceder a memória ou apresentar latência excessiva:

```text
llama3.2:3b
```

Essa alternativa tem aproximadamente 2 GB no catálogo Ollama e é adequada para tarefas menores de instrução, resumo e recuperação, mas deve ser comparada com Qwen3 4B em português e em extração estruturada.

## Decisão provisória

```text
Provider local: Ollama
LLM inicial: qwen3:4b
Embedding inicial: nomic-embed-text:v1.5
Topologia: um Ollama compartilhado, duas stacks de memória isoladas
```

A decisão final depende dos testes funcionais e de consumo no `hermes-lab`.

## Fontes

- Ollama Qwen3: https://ollama.com/library/qwen3
- Ollama Qwen3 4B: https://ollama.com/library/qwen3:4b
- Ollama Llama 3.2: https://ollama.com/library/llama3.2
- Ollama nomic-embed-text: https://ollama.com/library/nomic-embed-text
- Mem0 local com Ollama: https://docs.mem0.ai/cookbooks/companions/local-companion-ollama
- Mem0 compatibilidade OpenAI: https://docs.mem0.ai/open-source/features/openai-compatibility
