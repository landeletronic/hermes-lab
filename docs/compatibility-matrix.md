# Matriz de compatibilidade verificada

**Host:** Linux x86_64
**Validação:** 2026-11-01
**Política:** nenhum deployment usará `latest`, `stable` ou `main`.

| Componente | Pin selecionado | Digest/revisão | Estado |
|---|---|---|---|
| Hermes Agent | `nousresearch/hermes-agent:v0.22.5` | `sha256:fd822bae0a61bf023450737edbeaeea5fc6460f17cb4ee240a7e67b306ed63fb` | manifesto Docker verificado e imagem puxada no host |
| Honcho base | `ghcr.io/plastic-labs/honcho:v3.5.0` | `sha256:48c08d96b96d9a8012e6200bd9aa1dd070a96fb1c417fe37cd2ca40650c809e1` | base verificada; imagem local construída com tokenizers pré-carregados |
| Honcho local | `hermes-lab-honcho:3.5.0-local` | `sha256:491ec67c4f3125fa3a2786fd8b14387b1c9fa796eb1509fed60c99bcd4162788` | build local verificado; API saudável; deriver interrompido após teste de latência |
| PostgreSQL Honcho | `pgvector/pgvector:pg15` | `sha256:3a780571a333f6b7a3142bc7bfdd48bc9536f664e0ff74f48499d0a5d50e39ea` | manifesto Docker verificado e imagem puxada no host |
| PostgreSQL Mem0 | `pgvector/pgvector:pg17` | `sha256:2017da03379b27508603c44e9041a082ef53ef711fed614ccd5044eaf9df6e31` | manifesto Docker verificado e imagem puxada no host |
| Redis Honcho | `redis:8.2` | `sha256:8faf1ffc0fa573b9b0f5a8f886e45e27b498cdd5e9c7ee3c611856d30d0d4401` | manifesto Docker verificado e imagem puxada no host |
| Mem0 server | repositório oficial | `753496cf1435d76c3a711f3af51303f7fdcb6420` | modo HTTP com Ollama bloqueado no commit inspecionado; usar OSS |
| Mem0 plugin | `integrations/hermes-plugin-mem0` | commit do checkout Mem0 | OSS com Ollama e pgvector ainda precisa de teste Hermes ponta a ponta |
| Mem0 Python SDK | `v2.2.1` | `94c3fe9f238f3dbf29c9ce98643bd71eb13077cd` | release oficial verificada |
| Docker Engine | pacote Ubuntu | `29.1.3-0ubuntu4.1` | instalado e validado |
| Docker Compose | pacote Ubuntu | `2.40.3+ds1-0ubuntu1` | instalado e validado |
| Ollama LLM operacional | `llama3.2:3b` | `a80c4f17acd55265feec403c7aef86be0c25983ab279d83f3bcd3abbcb5b8b72` | validado no host; resposta curta em ~7 s |
| Ollama LLM fallback | `qwen3:4b` | `359d7dd4bcdab3d86b87d73ac27966f4dbb9f5efdfcc75d34a8764a09474fae7` | validado no host; resposta curta em ~46 s |
| Ollama embeddings | `nomic-embed-text:v1.5` | `0a109f422b47e3a30ba2b10eca18548e944e8a23073ee3f3e947efcf3c45e59f` | dimensão validada: 768 |

## Observações

- Os digests acima são para a plataforma `linux/amd64` do host.
- O servidor Mem0 não possuiu uma release/tag específica confirmada para o caminho self-hosted; por isso será construído a partir de commit imutável do repositório oficial.
- Antes do deploy, cada imagem deve ser puxada, inspecionada e registrada no lock final.
- O provider local selecionado é Ollama, com `llama3.2:3b` como modelo operacional, `qwen3:4b` como fallback e `nomic-embed-text:v1.5` para embeddings.
- O endpoint de geração e o endpoint de embeddings foram exercitados no host.
- A dimensão validada do embedding é 768.
- A integração com Honcho e Mem0 ainda não foi validada.
