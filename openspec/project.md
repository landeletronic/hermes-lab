# OpenSpec — Hermes Lab

## Objetivo

Especificar e validar uma infraestrutura reproduzível para comparar memória de longo prazo no Hermes Agent da Nous Research.

## Componentes

- `hermes-honcho`: Hermes Webapp + plugin Honcho.
- `honcho`: API, deriver e componentes auxiliares oficiais.
- `postgres-honcho`: PostgreSQL com pgvector exclusivo.
- `hermes-mem0`: Hermes Webapp + plugin Mem0.
- `mem0`: servidor Mem0 self-hosted.
- `postgres-mem0`: PostgreSQL/pgvector exclusivo.

## Invariantes

- Mesmo modelo nos dois Hermes: `GPT-5.6-luna`.
- Nenhum estado persistente dentro do checkout Git.
- Nenhum serviço usa `latest`, `stable` ou `main`.
- PostgreSQL e serviços de memória não são publicados na LAN.
- Webapps têm autenticação e portas externas distintas.
- Toda imagem deve ter versão e digest registrados antes do deploy.
- Todo banco deve ter backup e teste de restauração documentados.

## Critérios de aceitação

- Docker Engine e Compose instalados e versionados no host.
- Imagens selecionadas em fontes oficiais e fixadas por digest.
- Health checks passam para cada serviço.
- Webapp Honcho acessível na porta designada e autenticado.
- Webapp Mem0 acessível na porta designada e autenticado.
- Memórias persistem após reinício.
- Memórias não atravessam as duas stacks.
- Backup e restauração funcionam em ambiente de teste.
- Repositório público não contém segredos ou dados operacionais sensíveis.
