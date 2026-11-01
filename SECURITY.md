# Segurança

Este repositório será publicado como público no GitHub. Portanto, todo conteúdo deve ser considerado potencialmente visível para terceiros.

## Nunca versionar

- Chaves de API e tokens OAuth.
- Senhas de PostgreSQL, Honcho, Mem0 ou Hermes Webapp.
- Chaves privadas SSH/TLS.
- Cookies, tokens de sessão e arquivos de autenticação.
- Bancos de dados, dumps, embeddings e transcrições reais.
- Endereços internos sensíveis quando não forem necessários à documentação.

## Princípios

1. Segredos ficam em `.env` ou em um secret manager, nunca em YAML/JSON versionado.
2. Arquivos de exemplo usam valores fictícios e claramente inválidos.
3. Cada serviço recebe apenas as permissões necessárias.
4. Honcho e Mem0 usam bancos PostgreSQL separados.
5. Portas de administração ficam restritas à LAN ou VPN.
6. O Webapp não será exposto diretamente à internet.
7. Imagens Docker serão fixadas por versão; evitar depender permanentemente de `latest`.
8. Logs devem ser revisados antes de qualquer commit.
9. Dados de teste devem ser sintéticos ou anonimizados.

## Antes de publicar

- Executar busca por segredos no histórico e nos arquivos atuais.
- Revisar `.gitignore`.
- Conferir o conteúdo de `git diff --cached`.
- Confirmar que não há `.env`, banco, backup ou transcrição no commit.
- Preferir GitHub Actions com secrets do repositório para automações.
