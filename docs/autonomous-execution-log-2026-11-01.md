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

Não existem credenciais de provedor nem um `provider_model_id` confirmado no host. O modelo lógico definido é `GPT-5.6-luna`, mas a API/provider exige um identificador técnico e uma chave configurados fora do Git.

Honcho e Mem0 também precisam de credenciais/configuração de LLM para derivação, embeddings e recursos de memória. Não iniciar containers de aplicação com placeholders, porque isso produziria uma implantação falsa ou falha de health check.

## Próxima ação necessária

Criar os arquivos de secrets diretamente no host, com permissões `600`, contendo a chave do provider escolhido e o model ID exato. As chaves não devem ser enviadas pelo chat nem versionadas.

Após essa configuração, a execução poderá continuar com:

1. validação do provider/modelo;
2. criação dos Compose fixados;
3. configuração do firewall;
4. build do servidor Mem0 no commit definido;
5. subida gradual das duas stacks;
6. testes de saúde, persistência, isolamento e restauração.
