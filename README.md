# Hermes Lab

Laboratório para comparar memória de longo prazo no **Hermes Agent da Nous Research**.

## Ambientes planejados

- **Hermes + Honcho** — Webapp dedicado, porta definida durante a implementação.
- **Hermes + Mem0** — Webapp dedicado, porta definida durante a implementação.

Os ambientes devem usar o mesmo modelo (`GPT-5.6-luna`) e permanecer isolados em containers, volumes, configurações e bancos de dados separados.

## Objetivos

1. Executar o Hermes Agent em containers Docker no servidor `hermes-lab`.
2. Executar Honcho self-hosted com PostgreSQL.
3. Executar Mem0 self-hosted com PostgreSQL.
4. Disponibilizar uma interface Webapp independente para cada ambiente.
5. Comparar persistência, recuperação, atualização, latência e qualidade da memória.
6. Manter o projeto seguro para publicação em repositório GitHub público.

## Segurança

- Nunca versionar chaves, tokens, senhas, cookies, bancos ou transcrições reais.
- Configurações locais devem partir de arquivos `.env.example` sem valores secretos.
- Dados persistentes devem ficar fora do repositório ou ser explicitamente ignorados.
- O Webapp deve exigir autenticação e ser limitado à LAN/VPN.
- Toda alteração operacional deve ser documentada antes ou junto do código/configuração correspondente.

## Documentação

- [`docs/architecture.md`](docs/architecture.md) — arquitetura e decisões.
- [`docs/plan.md`](docs/plan.md) — plano de implementação e testes.
- [`docs/research-hermes-honcho-mem0.md`](docs/research-hermes-honcho-mem0.md) — pesquisa consolidada e decisões de comparação.
- [`SECURITY.md`](SECURITY.md) — regras de segurança e publicação.

## Status

Fase atual: planejamento e preparação do repositório local.
