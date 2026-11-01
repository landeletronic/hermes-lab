# OpenSpec — Stack Mem0 self-hosted

## Decisão de implementação

O servidor HTTP self-hosted do Mem0 foi inspecionado no commit `753496cf1435d76c3a711f3af51303f7fdcb6420`.

Nesse commit, o servidor bundled aceita somente os providers `openai`, `anthropic` e `gemini` no caminho padrão. Portanto, não será iniciado um servidor Mem0 HTTP configurado como Ollama sem uma alteração de código própria e revisão adicional.

A implementação planejada passa a ser:

```text
Hermes Mem0 → plugin/SDK Mem0 OSS local → Ollama
                                      └→ PostgreSQL/pgvector exclusivo
```

Isso continua sendo self-hosted, mantém PostgreSQL/pgvector dedicado e evita API externa. A alternativa HTTP permanece registrada como bloqueada até existir uma extensão testada e fixada.

## Critérios de aceitação

1. Plugin Mem0 OSS é instalado a partir de versão/commit fixado.
2. Provider LLM local é Ollama.
3. Provider de embedding local é Ollama com dimensão 768.
4. PostgreSQL/pgvector é exclusivo da stack Mem0.
5. Nenhum serviço Mem0 ou PostgreSQL é publicado na LAN.
6. Add, search, update e delete funcionam com dados sintéticos.
7. Estado persiste após reinício controlado.
8. A stack não acessa o banco ou volume Honcho.

## Bloqueio do modo HTTP

O modo HTTP Mem0 somente poderá ser usado se:

- o projeto upstream adicionar provider Ollama ao servidor;
- ou for criado um fork próprio com testes, lockfile, SBOM e imagem por digest;
- e a API for validada ponta a ponta com o plugin Hermes.

Não usar `AUTH_DISABLED=true` nem iniciar o servidor HTTP com provider incompatível.
