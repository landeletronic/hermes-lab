# Execução autônoma — progresso 2026-11-01

## Concluído

- Pesquisa paralela de Hermes, Honcho, Mem0 e segurança.
- Docker Engine instalado e validado.
- Docker Compose V2 instalado e validado.
- Imagens Hermes, Honcho, PostgreSQL/pgvector e Redis puxadas por digest amd64.
- Diretórios persistentes criados em `/srv/hermes-lab`.
- Matriz de compatibilidade criada.
- Manifesto e especificações OpenSpec criados.
- Inventário público sanitizado para remover endereços e identificadores operacionais.
- Histórico de alterações versionado e publicado no GitHub.

## Bloqueio atual

A estratégia de autenticação do Hermes foi definida como **OpenAI Codex OAuth**, usando o login/assinatura do ChatGPT, sem chave de API avulsa para o Hermes principal.

Isso remove a necessidade de `OPENAI_API_KEY` no container do Hermes, mas não resolve automaticamente as credenciais internas de Honcho e Mem0. Esses serviços executam derivação, embeddings e processamento de memória próprios e precisam de um provider configurado separadamente — por API, endpoint compatível ou modelo local.

Também precisamos validar se `GPT-5.6-luna` é um `provider_model_id` aceito pelo backend `openai-codex`. O nome lógico foi preservado, mas não será assumido como identificador técnico.

## Próxima ação necessária

1. Autenticar o Hermes via fluxo OAuth/Codex em uma sessão interativa segura no host ou por túnel autorizado.
2. Confirmar o modelo disponível no catálogo Codex.
3. Definir como Honcho e Mem0 obterão LLM/embeddings sem expor uma chave no Git — API separada, endpoint compatível ou modelos locais.
4. Só então iniciar os containers de aplicação.
