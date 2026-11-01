# Plano de execução autônoma — Hermes Lab v2

**Status:** execução autorizada pelo usuário
**Perfil de execução:** DevOps sênior, com evidências verificáveis, isolamento, rollback e documentação contínua.

## Objetivo

Implantar no host Linux duas stacks Docker isoladas para comparar memória de longo prazo no **Hermes Agent da Nous Research**:

```text
Stack A — Hermes + Honcho self-hosted + PostgreSQL/pgvector
Stack B — Hermes + Mem0 self-hosted + PostgreSQL/pgvector
```

Ambas usarão um serviço Ollama local compartilhado para processamento de memória:

```text
Ollama
├── llama3.2:3b             # LLM operacional inicial
├── qwen3:4b                # fallback para avaliar qualidade/português
└── nomic-embed-text:v1.5   # embeddings
```

O Hermes principal poderá usar Codex OAuth/ChatGPT, mas Honcho e Mem0 não dependerão automaticamente dessa autenticação.

## Estado já confirmado

- Repositório público: `landeletronic/hermes-lab`.
- Host Linux com 4 vCPUs.
- Aproximadamente 7,2 GiB de RAM.
- Swap de 8 GiB.
- Docker Engine instalado e validado.
- Docker Compose V2 instalado e validado.
- QEMU Guest Agent ativo.
- Diretórios de dados criados em `/srv/hermes-lab`.
- Imagens base fixadas por versão e digest amd64.
- Repositório com Manifesto e OpenSpec inicial.

## Regras obrigatórias

1. Não usar tags `latest`, `stable` ou `main` em deployment.
2. Fixar versão e digest de imagens.
3. Usar somente fontes oficiais ou commits revisados.
4. Não versionar segredos, bancos, logs, sessões, embeddings ou transcrições reais.
5. Manter dados persistentes fora do checkout Git.
6. Usar redes, volumes, bancos e credenciais separados por stack.
7. Não publicar PostgreSQL, Redis, pgvector, Ollama ou APIs de memória na LAN.
8. Publicar somente os Webapps necessários, com autenticação.
9. Não montar `/var/run/docker.sock` nos containers.
10. Usar limites de CPU, memória, PIDs e logs.
11. Aplicar `cap_drop`, `no-new-privileges` e filesystem read-only quando compatíveis.
12. Fazer backup antes de migração, upgrade ou alteração de schema.
13. Validar cada mudança no estado real antes de declarar sucesso.
14. Usar dados sintéticos nos testes comparativos.
15. Parar diante de risco de perda de dados, credencial ausente ou incompatibilidade não resolvida.

## Governança do projeto

### Manifesto

Manter `MANIFESTO.md` como fonte dos princípios de segurança, reprodutibilidade, isolamento e evidência.

### OpenSpec

Manter e expandir:

```text
openspec/
├── project.md
├── specs/
│   ├── versions.yaml
│   ├── security.md
│   ├── host.md
│   ├── ollama.md
│   ├── honcho-stack.md
│   ├── mem0-stack.md
│   ├── webapp.md
│   ├── backups.md
│   └── testing.md
└── changes/
```

Cada especificação deve conter objetivo, pré-condições, configuração, critérios de aceitação, riscos, rollback e evidências esperadas.

## Uso de subagentes

### Subagente A — compatibilidade

Pesquisar fontes primárias e confirmar versões, configurações e compatibilidade de:

- Hermes Webapp e plugin de memória;
- Honcho self-hosted;
- Mem0 self-hosted;
- Ollama e endpoint OpenAI-compatible;
- PostgreSQL/pgvector;
- Redis, deriver e workers.

Não alterar o host. Entregar matriz de compatibilidade com fontes e riscos.

### Subagente B — segurança e supply chain

Revisar:

- histórico Git;
- segredos e dados internos;
- imagens e digests;
- permissões;
- firewall;
- exposição de portas;
- Compose, capabilities e volumes;
- necessidade de SBOM, Gitleaks e TruffleHog.

Não alterar arquivos sem registrar a recomendação.

### Subagente C — arquitetura Docker

Projetar:

- Compose por stack;
- serviço Ollama compartilhado;
- redes internas;
- volumes fora do Git;
- health checks;
- limites de recursos;
- restart policies;
- backup sidecars ou jobs;
- portas externas dos Webapps.

### Subagente D — testes

Criar matriz de testes para:

- instalação;
- health checks;
- autenticação;
- geração local;
- embeddings;
- captura de memória;
- recuperação semântica;
- atualização e remoção;
- isolamento entre stacks;
- persistência após reinício;
- backup/restauração;
- falha e recuperação;
- consumo de CPU/RAM/disco.

### Coordenação

Subagentes não terão autorização para publicar, alterar firewall, manipular segredos ou declarar implantação concluída. A coordenação principal valida suas conclusões e executa mudanças externas.

## Fases de execução

### Fase 0 — preflight e segurança

1. Ler o estado atual do repositório e do host.
2. Confirmar branch, remote, working tree e arquivos ignorados.
3. Rodar secret scan no working tree e histórico.
4. Confirmar sudo, Docker, Compose, recursos, volumes e portas.
5. Verificar se há arquivos locais novos não autorizados para publicação.
6. Criar checkpoint Git e registrar evidências.

**Saída:** preflight aprovado ou bloqueio documentado.

### Fase 1 — especificação e pins

1. Consolidar resultados dos subagentes.
2. Fixar versões e digests de:
   - Hermes;
   - Honcho;
   - Mem0;
   - PostgreSQL/pgvector;
   - Redis;
   - Ollama;
   - modelos `qwen3:4b` e `nomic-embed-text:v1.5`.
3. Confirmar compatibilidade de API e formato de embeddings.
4. Atualizar `openspec/specs/versions.yaml`.
5. Criar a matriz de compatibilidade.
6. Não avançar com placeholders de versão.

**Critério:** toda imagem e modelo de deployment possui pin verificável.

### Fase 2 — preparação do host

1. Confirmar Docker e Compose.
2. Criar ou validar `/srv/hermes-lab`.
3. Definir ownership e permissões mínimas.
4. Configurar diretórios de:
   - dados Honcho;
   - dados Mem0;
   - PostgreSQL Honcho;
   - PostgreSQL Mem0;
   - Ollama;
   - backups;
   - logs.
5. Instalar/configurar firewall local.
6. Permitir SSH somente de origem administrativa autorizada.
7. Reservar Webapps somente para LAN/VPN.
8. Confirmar que bancos e Ollama permanecem internos.

**Critério:** host pronto sem serviços de aplicação expostos.

### Fase 3 — Ollama local

1. Escolher instalação containerizada ou nativa conforme pin reproduzível.
2. Fixar imagem/binário do Ollama.
3. Iniciar Ollama somente na rede interna.
4. Baixar `llama3.2:3b`, `qwen3:4b` e `nomic-embed-text:v1.5`.
5. Registrar digests dos modelos.
6. Testar geração com o modelo operacional e o fallback.
7. Testar embeddings e dimensão retornada.
8. Medir RAM, CPU, latência e tamanho em disco.
9. Configurar `OLLAMA_MAX_LOADED_MODELS=1`.
10. Definir limites de memória.

**Critério:** Ollama responde geração e embedding sem chave externa.

### Fase 4 — Stack Honcho

1. Criar rede exclusiva da stack.
2. Criar PostgreSQL com pgvector e volume próprio.
3. Criar Redis e worker/deriver conforme requisitos.
4. Configurar Honcho para usar Ollama via endpoint compatível.
5. Validar extração, representação e persistência.
6. Configurar plugin Honcho no Hermes.
7. Configurar Hermes Webapp Honcho.
8. Configurar autenticação.
9. Publicar somente a porta Webapp designada.
10. Executar health checks e teste funcional.

**Critério:** memória Honcho funciona sem provider externo.

### Fase 5 — Stack Mem0

1. Criar rede exclusiva da stack.
2. Criar PostgreSQL/pgvector próprio.
3. Fixar e construir o servidor Mem0 a partir de commit imutável.
4. Configurar Mem0 para usar Ollama via endpoint compatível, se validado.
5. Configurar embedding local e dimensão correta.
6. Configurar plugin Mem0 no Hermes.
7. Configurar Hermes Webapp Mem0.
8. Configurar autenticação.
9. Publicar somente a porta Webapp designada.
10. Executar health checks e teste funcional.

**Critério:** memória Mem0 funciona sem provider externo.

### Fase 6 — comparação controlada

1. Usar o mesmo roteiro nos dois ambientes.
2. Usar dados sintéticos idênticos.
3. Testar fatos, preferências, correções e buscas indiretas.
4. Encerrar e reabrir sessões.
5. Medir persistência, recuperação, latência e consumo.
6. Verificar que nenhuma memória atravessa as stacks.
7. Registrar resultados sem declarar vencedor antes dos testes.

### Fase 7 — backup e recuperação

1. Criar backup lógico de cada PostgreSQL.
2. Fazer cópia fora dos volumes ativos.
3. Proteger backups com permissões e, se possível, criptografia.
4. Restaurar cada banco em ambiente separado.
5. Validar dados e funcionamento após restauração.
6. Documentar RPO, RTO e retenção.

### Fase 8 — publicação e fechamento

1. Revisar Compose, Dockerfiles, OpenSpec e manifesto.
2. Executar secret scan no working tree e histórico.
3. Confirmar que nenhum arquivo local sensível está rastreado.
4. Executar `git diff --cached --check`.
5. Fazer commits pequenos e descritivos.
6. Publicar no GitHub.
7. Ler o estado remoto e verificar os commits.
8. Entregar relatório final com evidências, riscos e pendências.

## Critérios de conclusão

O plano só será concluído quando:

- Ollama local estiver funcionando;
- os modelos estiverem fixados e verificados;
- Honcho e Mem0 estiverem funcionando com Ollama;
- os dois Webapps estiverem acessíveis pela LAN/VPN;
- autenticação estiver ativa;
- bancos estiverem isolados;
- memórias persistirem e forem recuperadas;
- nenhum dado cruzar entre stacks;
- backup/restauração forem testados;
- firewall e portas forem verificados;
- documentação e OpenSpec estiverem atualizados;
- GitHub estiver sincronizado;
- working tree estiver limpa.

## Condições de parada

A execução deve parar e registrar o bloqueio se:

- o endpoint Ollama não for compatível com Honcho ou Mem0;
- o consumo exceder a capacidade segura do host;
- forem necessários dados ou credenciais não disponíveis;
- houver risco de perda de banco ou volume;
- uma imagem não puder ser fixada por digest;
- a autenticação do Webapp não puder ser configurada;
- for necessário expor serviços à internet pública.

## Autorização

A execução começa somente após confirmação explícita:

> Autorizo a execução autônoma do plano `docs/autonomous-execution-plan-v2.md`, dentro do escopo e das regras descritas.
