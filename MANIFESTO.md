# Manifesto do Hermes Lab

## Princípios

1. **Segurança por padrão:** nenhuma interface, banco ou segredo será exposto sem necessidade documentada.
2. **Reprodutibilidade:** imagens, pacotes e dependências usarão versões fixadas; `latest`, `stable` e `main` são proibidos em deployments reproduzíveis.
3. **Isolamento:** Honcho e Mem0 terão redes, volumes, credenciais, históricos e PostgreSQL separados.
4. **Segredos fora do Git:** chaves, senhas, tokens, bancos, logs e transcrições reais nunca serão versionados.
5. **Evidência:** nenhuma tarefa será considerada concluída sem verificação do estado real.
6. **Dados sintéticos:** os testes comparativos não usarão dados pessoais reais.
7. **Menor privilégio:** containers, usuários, volumes e portas terão somente as permissões necessárias.
8. **Documentação contínua:** alterações de host, infraestrutura e configuração serão registradas em documentos versionados.
9. **Rollback:** upgrades e migrações exigirão backup verificável e procedimento de retorno.
10. **Transparência:** decisões, limitações e resultados negativos serão registrados sem inventar evidências.

## Escopo

O projeto compara o Hermes Agent da Nous Research com Honcho e Mem0 self-hosted, usando o mesmo modelo `GPT-5.6-luna` e interfaces Webapp independentes.

## Proibições

- Não usar tags Docker flutuantes.
- Não publicar PostgreSQL, Redis, pgvector ou APIs de memória na LAN sem justificativa.
- Não montar `/var/run/docker.sock` nos containers do laboratório.
- Não colocar credenciais em Compose, Dockerfile, Markdown ou argumentos públicos.
- Não expor o Webapp sem autenticação.
