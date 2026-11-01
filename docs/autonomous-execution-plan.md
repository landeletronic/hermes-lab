# Plano de execução autônoma — Hermes Lab

**Status:** aguardando autorização explícita do usuário
**Escopo:** concluir a especificação e implementar o laboratório no `hermes-lab`.

## Objetivo

Preparar no host `hermes-lab` duas stacks Docker isoladas para comparar memória de longo prazo no Hermes Agent da Nous Research:

```text
Stack A: Hermes Webapp + Honcho self-hosted + PostgreSQL/pgvector
Stack B: Hermes Webapp + Mem0 self-hosted + PostgreSQL/pgvector
```

Ambas devem usar o mesmo modelo configurado como `GPT-5.6-luna`, portas Webapp distintas e dados completamente separados.

## Escopo autorizado após aprovação

- executar comandos administrativos via SSH no `hermes-lab`;
- instalar e configurar Docker Engine e Docker Compose;
- criar diretórios persistentes em `/srv/hermes-lab`;
- criar arquivos Docker Compose e configurações sanitizadas no repositório;
- criar volumes, redes e bancos isolados;
- instalar/configurar Hermes, Honcho e Mem0 conforme documentação oficial;
- configurar autenticação e acesso restrito à LAN/VPN;
- iniciar serviços e executar testes de saúde;
- documentar cada alteração;
- fazer commits e publicar os commits no GitHub já configurado.

## Fora do escopo sem nova autorização

- exposição à internet pública;
- alteração de regras do roteador ou firewall externo;
- envio de dados reais ou transcrições para serviços externos;
- uso de chaves privadas fornecidas pelo chat;
- destruição de dados existentes;
- criação de contas externas;
- alteração do modelo escolhido sem registrar a decisão;
- publicação de segredos no GitHub.

## Estratégia de subagentes

### Subagente 1 — documentação e compatibilidade

Pesquisar somente fontes primárias e verificar:

- versão atual do Hermes Docker;
- requisitos do Webapp;
- integração Hermes/Honcho;
- requisitos do Honcho self-hosted;
- integração Hermes/Mem0;
- requisitos do Mem0 self-hosted;
- compatibilidade PostgreSQL/pgvector/Redis;
- flags e variáveis de autenticação.

Entregável: matriz de versões, requisitos e riscos com URLs/fontes.

### Subagente 2 — segurança e revisão do repositório

Verificar:

- `.gitignore`;
- arquivos `.env.example`;
- possibilidade de segredos no histórico;
- dados internos em documentação;
- permissões recomendadas;
- exposição de portas;
- política de backup.

Entregável: checklist de segurança e correções sugeridas.

### Subagente 3 — desenho Docker/Compose

Projetar:

- uma Compose por stack;
- redes isoladas;
- volumes fora do Git;
- limites de CPU/RAM;
- health checks;
- ordem de inicialização;
- portas externas `9121` e `9122`;
- PostgreSQL não publicado na LAN.

Entregável: desenho técnico e arquivos Compose propostos.

### Subagente 4 — testes e critérios de aceitação

Criar roteiro para validar:

- inicialização;
- autenticação;
- acesso pelo Windows;
- persistência após reinício;
- isolamento Honcho/Mem0;
- gravação e recuperação de memória;
- atualização e remoção de memória;
- falha/reconexão do serviço de memória;
- consumo de recursos.

Entregável: matriz de testes reproduzível.

## Execução sequencial

### Etapa 0 — preflight

1. Ler o estado atual do repositório.
2. Confirmar branch, remote e working tree.
3. Verificar novamente Docker, Compose, sudo, CPU, RAM, swap, armazenamento e portas.
4. Criar um ponto de restauração documental.
5. Não alterar serviços antes de concluir as verificações.

### Etapa 1 — consolidar especificação

1. Reunir os resultados dos subagentes.
2. Escolher versões fixas e imagens/digests.
3. Definir portas, autenticação e limites de recursos.
4. Atualizar `docs/architecture.md`, `docs/plan.md` e documentação operacional.
5. Executar revisão de segurança.
6. Fazer commit apenas da especificação.

### Etapa 2 — preparar host

1. Validar Docker Engine e Compose.
2. Criar `/srv/hermes-lab` e subdiretórios.
3. Definir ownership e permissões mínimas.
4. Configurar firewall local para SSH e Webapps apenas na LAN autorizada.
5. Registrar o estado antes/depois.

### Etapa 3 — implementar stack Honcho

1. Criar rede e volumes exclusivos.
2. Criar PostgreSQL com pgvector.
3. Criar Redis/worker se exigido pela versão selecionada.
4. Subir Honcho e validar health check.
5. Criar Hermes Webapp Honcho.
6. Configurar `GPT-5.6-luna` sem versionar a chave.
7. Validar login e acesso pela porta `9121`.

### Etapa 4 — implementar stack Mem0

1. Criar rede e volumes exclusivos.
2. Criar PostgreSQL com pgvector.
3. Subir Mem0 self-hosted.
4. Criar Hermes Webapp Mem0.
5. Configurar `GPT-5.6-luna` sem versionar a chave.
6. Validar login e acesso pela porta `9122`.

### Etapa 5 — validação integrada

1. Executar todos os health checks.
2. Validar portas e firewall.
3. Reiniciar cada stack e confirmar persistência.
4. Executar o roteiro de memória com dados sintéticos.
5. Confirmar que as memórias não atravessam as stacks.
6. Medir CPU, RAM, disco e latência.
7. Fazer backup e teste de restauração.
8. Registrar evidências nos documentos.

### Etapa 6 — publicação e fechamento

1. Executar busca por segredos no estado atual e no histórico.
2. Revisar diffs e arquivos gerados.
3. Atualizar README, arquitetura, inventário, segurança e operação.
4. Criar commits pequenos e descritivos.
5. Fazer push para `origin/main`.
6. Ler o estado remoto e confirmar os commits publicados.
7. Entregar relatório final com o que foi concluído, evidências e pendências.

## Regras de autonomia

- Não utilizar tags flutuantes como `latest`, `stable` ou `main` em produção/laboratório reproduzível. Fixar versões compatíveis e, quando disponível, digest imutável para cada imagem; registrar a matriz de compatibilidade antes do deploy.
- Não inventar versões, comandos, resultados ou compatibilidade.
- Usar fontes oficiais para decisões técnicas.
- Parar se a documentação oficial contradizer o plano.
- Parar se houver risco de perda de dados.
- Parar se for necessário expor um serviço à internet.
- Não solicitar nem registrar senhas no chat.
- Verificar toda mudança externa após executá-la.
- Manter o GitHub público livre de segredos e dados reais.
- Usar subagentes para tarefas independentes de pesquisa, segurança, Compose e testes; a execução privilegiada continuará sob coordenação principal.

## Critérios de conclusão

O trabalho somente será considerado concluído quando:

- ambas as stacks estiverem ativas;
- os dois Webapps forem acessíveis pelo Windows;
- autenticação estiver ativa;
- Honcho e Mem0 estiverem usando bancos isolados;
- Hermes estiver usando `GPT-5.6-luna` em ambas;
- persistência e recuperação tiverem sido verificadas;
- backup/restauração tiverem sido testados;
- documentação estiver atualizada;
- GitHub estiver sincronizado;
- não houver segredos no repositório;
- working tree estiver limpa.

## Autorização

A execução autônoma deve começar somente após a confirmação explícita do usuário, por exemplo:

> Autorizo a execução autônoma do plano `docs/autonomous-execution-plan.md`, dentro do escopo e das regras descritas.
