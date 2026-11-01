# Comparativo de autenticação e conectividade

## Contexto

O projeto usa o **Hermes Agent da Nous Research** com duas integrações de memória de longo prazo:

```text
Hermes + Honcho
Hermes + Mem0
```

A preferência é autenticar o Hermes principal usando o login OAuth do OpenAI Codex/ChatGPT, evitando uma chave de API avulsa para o Hermes.

## Alternativas analisadas

### 1. Credenciais separadas

```text
Hermes
└── OpenAI Codex OAuth / assinatura ChatGPT

Honcho
└── credencial própria para LLM e embeddings

Mem0
└── credencial própria para LLM e embeddings
```

#### Vantagens

- Menor complexidade operacional.
- Compatibilidade mais direta com as integrações oficiais.
- Diagnóstico mais simples.
- Melhor isolamento entre Hermes, Honcho e Mem0.
- Uma falha em um serviço não precisa interromper os demais.
- Permite escolher modelos diferentes para geração, extração e embeddings.
- Não adiciona um proxy intermediário.

#### Desvantagens

- Mais de uma credencial precisa ser protegida e rotacionada.
- Pode haver cobrança separada de API.
- Honcho e Mem0 não aproveitam automaticamente a assinatura do ChatGPT.
- Os serviços de memória precisam de suas próprias configurações de LLM e embeddings.

## 2. Endpoint OpenAI-compatible

```text
Hermes
└── OpenAI Codex OAuth / assinatura ChatGPT

Honcho ─┐
        ├── endpoint OpenAI-compatible
Mem0 ───┘
```

O endpoint pode ser um gateway próprio, LiteLLM ou outro serviço compatível com a API OpenAI.

#### Vantagens

- Centraliza roteamento, políticas, logs e limites.
- Pode esconder a credencial real dos containers.
- Facilita trocar o provider sem alterar Honcho e Mem0.
- Pode oferecer retries, fallback e rate limiting.
- Padroniza a integração dos serviços consumidores.

#### Desvantagens

- Adiciona outro serviço para operar e atualizar.
- Cria um ponto único de falha.
- Pode haver incompatibilidade com streaming, JSON estruturado, tool calling ou embeddings.
- O endpoint precisa suportar tanto LLM quanto embeddings.
- A autenticação Codex/ChatGPT não deve ser presumida como reutilizável por Honcho e Mem0.
- O proxy oficial do Hermes documentado atualmente não deve ser assumido como compatível com OpenAI Codex sem validação explícita.

## Ponto técnico importante

O Hermes pode usar autenticação OAuth do Codex/ChatGPT. Isso resolve o acesso do **Hermes principal**.

Honcho e Mem0 executam processos próprios de:

- extração de fatos;
- geração de contexto;
- embeddings;
- busca semântica;
- processamento assíncrono.

Portanto, eles continuam precisando de um provider próprio, um endpoint compatível ou modelos locais.

O Mem0 documenta suporte a endpoints OpenAI-compatible por meio de configuração de base URL. Isso não elimina a necessidade de o endpoint fornecer autenticação e suportar as operações exigidas.

## Comparativo resumido

| Critério | Credenciais separadas | Endpoint OpenAI-compatible |
|---|---|---|
| Complexidade inicial | Menor | Maior |
| Isolamento | Melhor | Menor |
| Diagnóstico | Mais simples | Mais complexo |
| Rotação de credencial | Por serviço | Centralizada |
| Ponto único de falha | Não | Sim |
| Suporte a embeddings | Direto, conforme provider | Depende do endpoint |
| Reutilização da assinatura ChatGPT | Hermes/Codex | Não garantida para Honcho/Mem0 |
| Adequação ao primeiro teste | Alta | Média |
| Flexibilidade futura | Média | Alta |

## Recomendação para este laboratório

### Fase inicial

```text
Hermes    → Codex OAuth / assinatura ChatGPT
Honcho    → credencial própria ou modelo local
Mem0      → credencial própria ou modelo local
```

Essa opção reduz variáveis e facilita a comparação entre Honcho e Mem0.

### Fase posterior

```text
Hermes/Honcho/Mem0 → endpoint OpenAI-compatible controlado
```

O endpoint deve ser testado separadamente depois que as duas integrações funcionarem sem proxy.

## Alternativa sem chaves de API

Se o objetivo for não usar nenhuma chave de API, a alternativa é:

```text
Hermes  → Codex OAuth / ChatGPT
Honcho  → LLM e embeddings locais
Mem0    → LLM e embeddings locais
```

Essa solução reduz dependências externas, mas aumenta o consumo de CPU/RAM e pode reduzir a qualidade ou aumentar a latência. O host possui 4 vCPUs, portanto essa opção deve ser testada com limites de recursos.

## Decisão registrada

Para a primeira implementação:

1. usar Codex OAuth no Hermes;
2. não reutilizar automaticamente a sessão ChatGPT em Honcho/Mem0;
3. manter Honcho e Mem0 com providers independentes ou locais;
4. deixar endpoint OpenAI-compatible para uma segunda fase experimental;
5. não iniciar serviços com credenciais fictícias ou placeholders.

## Fontes consultadas

- Hermes Agent — LLM and Model Providers: https://hermes-agent.nousresearch.com/docs/integrations/providers
- Hermes Agent — Subscription Proxy: https://hermes-agent.nousresearch.com/docs/user-guide/features/subscription-proxy
- Hermes Agent — CLI Commands: https://hermes-agent.nousresearch.com/docs/reference/cli-commands
- Mem0 — Hermes integration: https://docs.mem0.ai/integrations/hermes
- Mem0 — OpenAI compatibility: https://docs.mem0.ai/open-source/features/openai-compatibility
- Honcho — self-hosting: https://docs.honcho.dev/v3/contributing/self-hosting
