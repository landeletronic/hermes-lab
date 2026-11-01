# Plano de implementação

## Fase 0 — Preparação

- [x] Criar repositório Git local.
- [x] Definir Hermes como Hermes Agent da Nous Research.
- [x] Definir Webapp como interface.
- [x] Definir Honcho self-hosted com PostgreSQL.
- [x] Definir Mem0 self-hosted com PostgreSQL.
- [x] Definir modelo comum: `GPT-5.6-luna`.
- [x] Criar regras iniciais para repositório público.

## Fase 1 — Inventário do host

- [x] Verificar Docker e Docker Compose — ainda não instalados.
- [x] Verificar recursos de CPU, RAM e armazenamento.
- [x] Verificar firewall e portas disponíveis.
- [x] Verificar versão do sistema operacional.
- [x] Definir diretórios de dados fora do checkout Git.

Diretórios planejados no host:

```text
/srv/hermes-lab/
├── honcho/
├── mem0/
├── postgres-honcho/
├── postgres-mem0/
├── backups/
└── logs/
```

Esses diretórios serão criados na fase de implementação, com permissões e ownership definidos conforme os containers. Nenhum dado persistente será armazenado no checkout Git.

**Fase 1 concluída.** Detalhes registrados em [`docs/inventory-hermes-lab.md`](inventory-hermes-lab.md).

## Fase 2 — Especificação

- [x] Fixar versões das imagens — tags e digests amd64 registrados em `openspec/specs/versions.yaml`.
- [x] Confirmar requisitos oficiais do Honcho self-hosted.
- [x] Confirmar requisitos oficiais do Mem0 self-hosted.
- [x] Definir PostgreSQL por stack — Honcho com pgvector/pg15; Mem0 com pgvector/pg17.
- [x] Definir autenticação e exposição somente à LAN/VPN.
- [x] Definir estratégia de backup.
- [x] Selecionar Ollama como provider local.
- [x] Selecionar `qwen3:4b` para extração/derivação inicial.
- [x] Selecionar `nomic-embed-text:v1.5` para embeddings.

A Fase 2 permanece aberta somente até validar os digests dos modelos Ollama, o endpoint OpenAI-compatible nos dois serviços de memória e o consumo real no host.

## Fase 3 — Implementação

- [ ] Criar Compose da stack Honcho.
- [ ] Criar Compose da stack Mem0.
- [ ] Configurar volumes persistentes.
- [ ] Configurar segredos sem versioná-los.
- [ ] Subir serviços e validar health checks.
- [ ] Configurar Hermes Webapp em portas distintas.

## Fase 4 — Validação

- [ ] Testar acesso pelo Windows.
- [ ] Testar autenticação.
- [ ] Confirmar persistência após reinício.
- [ ] Confirmar que as memórias não cruzam entre stacks.
- [ ] Confirmar captura e recuperação em cada provedor.
- [ ] Executar roteiro comparativo controlado.

## Fase 5 — Publicação

- [ ] Revisar arquivos e histórico Git em busca de segredos.
- [ ] Executar validações automatizadas.
- [ ] Criar documentação de operação.
- [ ] Criar repositório privado inicialmente, se necessário.
- [ ] Publicar no GitHub somente após revisão final.
