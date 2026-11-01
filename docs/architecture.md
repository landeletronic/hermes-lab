# Arquitetura — Hermes Lab

## Contexto

O laboratório compara dois provedores de memória de longo prazo integrados ao Hermes Agent da Nous Research.

## Topologia planejada

```text
Ubuntu Server 26.04.1 LTS / hermes-lab
│
├── Stack hermes-honcho
│   ├── Hermes Agent + Webapp
│   ├── Honcho self-hosted
│   ├── PostgreSQL exclusivo do Honcho
│   └── Porta Webapp dedicada
│
└── Stack hermes-mem0
    ├── Hermes Agent + Webapp
    ├── Mem0 self-hosted
    ├── PostgreSQL exclusivo do Mem0
    └── Porta Webapp dedicada
```

## Regras de isolamento

- Cada stack terá seu próprio Docker Compose.
- Cada stack terá rede, volumes e arquivos `.env` próprios.
- Os bancos PostgreSQL não compartilharão dados.
- Os históricos do Hermes não serão compartilhados.
- As duas instâncias usarão o mesmo modelo: `GPT-5.6-luna`.

## Interfaces

A interface escolhida é o Hermes Webapp. As portas ainda serão definidas após a inspeção das versões atuais do Hermes, Honcho, Mem0 e das imagens Docker.

## Variáveis deliberadamente pendentes

- Identificador efetivo aceito pela API para `GPT-5.6-luna`.
- Versões/imagens fixadas de Hermes, Honcho, Mem0 e PostgreSQL.
- Portas definitivas.
- Método de autenticação do Webapp.
- Política de backup e retenção.
