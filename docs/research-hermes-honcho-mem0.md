# Pesquisa e decisões — Hermes, Honcho e Mem0

> Documento consolidado a partir do estudo fornecido e de verificação posterior nas documentações oficiais. Afirmações que dependem de versão devem ser revalidadas durante a implementação.

## Escopo

Neste projeto, “Hermes” significa exclusivamente o **Hermes Agent da Nous Research**.

## Correções importantes do estudo original

- A interface web recomendada para este laboratório é o **Hermes Webapp**, servido pelo gateway; não devemos assumir uma porta `3333`.
- A documentação atual do Hermes diferencia **Dashboard** e **Webapp**. O Webapp é a interface escolhida para o laboratório e deve ser configurado com portas distintas para cada stack.
- O Webapp exige autenticação quando exposto em endereço não-localhost. Não devemos publicar a interface na LAN sem autenticação.
- A integração do Mem0 com Hermes não deve ser descrita como apenas MCP/SDK. Existe integração específica para Hermes, incluindo modo Cloud, servidor Mem0 self-hosted e modo OSS, sujeita à versão instalada.
- Não há base suficiente para afirmar que Honcho é “a recomendação oficial da equipe do Hermes”, “a queridinha dos usuários” ou que possui superioridade geral. Essas conclusões devem ser tratadas como opinião/marketing, não como requisito técnico.
- Benchmarks, número de estrelas, depoimentos e preços são temporais e não serão usados como critério único de decisão.

## Honcho

A integração do Honcho fornece memória persistente e modelagem do usuário/agente. A configuração oficial inclui plugin do Hermes, setup interativo e possibilidade de apontar para uma instância self-hosted.

Pontos relevantes para o laboratório:

- suporte a contexto persistente entre sessões;
- representação do usuário e do agente;
- busca e conclusões duráveis;
- configuração de workspace, peer e estratégia de sessão;
- possibilidade de usar Honcho self-hosted;
- PostgreSQL separado para isolamento do experimento.

## Mem0

A integração do Mem0 para Hermes oferece três modelos operacionais:

1. Mem0 Platform;
2. servidor Mem0 self-hosted;
3. modo OSS executado no processo do Hermes.

Para este laboratório será usado o **servidor Mem0 self-hosted com PostgreSQL próprio**, mantendo o armazenamento separado do Honcho.

Pontos relevantes:

- busca semântica;
- captura e extração de fatos;
- ferramentas de busca, adição, atualização e remoção;
- possibilidade de usar vector store e PostgreSQL conforme os requisitos da versão instalada;
- configuração independente por stack.

## Decisão do laboratório

A comparação será feita com:

```text
Stack A: Hermes + Honcho self-hosted + PostgreSQL A
Stack B: Hermes + Mem0 self-hosted + PostgreSQL B
```

Ambas usarão:

- Hermes Webapp;
- mesmo modelo: `GPT-5.6-luna`;
- mesma configuração de modelo;
- mesmo roteiro de testes;
- volumes e históricos independentes;
- autenticação própria;
- acesso limitado à LAN/VPN.

## Critérios de comparação

- persistência entre sessões;
- recuperação direta e semântica;
- atualização e correção de memórias;
- separação entre usuários/projetos;
- latência percebida;
- transparência dos dados armazenados;
- facilidade de backup e restauração;
- consumo de CPU, memória e armazenamento;
- dependências operacionais;
- comportamento em falha do serviço de memória;
- qualidade das respostas com e sem memória recuperada.

## Fontes a validar durante a implementação

- documentação Docker do Hermes Agent;
- documentação do Hermes Webapp e autenticação;
- documentação do plugin Honcho para Hermes;
- guia oficial do servidor Honcho self-hosted;
- documentação da integração Mem0 para Hermes;
- documentação do servidor Mem0 self-hosted e do backend PostgreSQL/vector store.

URLs de referência ficam na documentação do projeto, mas versões e comandos serão fixados somente após a inspeção do estado atual dos repositórios e imagens.
