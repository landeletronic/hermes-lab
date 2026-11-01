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

## Estado atual

O Hermes continuará podendo usar Codex OAuth/ChatGPT para a interface principal, mas o processamento de memória será local para evitar credenciais adicionais:

```text
Hermes  → Codex OAuth/ChatGPT ou provider configurado no Hermes
Honcho  → Ollama
Mem0    → Ollama
```

A opção de usar `GPT-5.6-luna` deixa de ser a configuração principal do laboratório. O foco agora é validar `qwen3:4b` e `nomic-embed-text:v1.5` no host.
## Nova decisão

A estratégia foi alterada para usar uma LLM local com Ollama, evitando credenciais adicionais para Honcho e Mem0:

- provider: Ollama;
- LLM operacional inicial: `llama3.2:3b`;
- fallback para comparação: `qwen3:4b`;
- embeddings: `nomic-embed-text:v1.5`;
- um serviço Ollama compartilhado pelas duas stacks;
- modelos e volumes fora do checkout Git.

Os digests foram registrados. A compatibilidade do endpoint OpenAI-compatible do Ollama com Honcho e Mem0 ainda será testada antes do deploy final.

## Resultado preliminar de desempenho

O teste CPU-only no host mostrou aproximadamente:

- `llama3.2:3b`: resposta curta em 7 segundos;
- `qwen3:4b`: resposta curta em 46 segundos e saída de raciocínio extensa.

Por isso, o modelo operacional inicial foi alterado para `llama3.2:3b`, mantendo `qwen3:4b` instalado como fallback para comparar qualidade de extração e português. A decisão pode ser revertida somente após medir a carga real de Honcho/Mem0.

## Próxima ação necessária

1. Instalar Ollama ou executar o serviço em container controlado.
2. Baixar os modelos fixados e registrar seus digests.
3. Validar geração e embeddings.
4. Validar Honcho e Mem0 contra o endpoint local.
5. Só então iniciar os Webapps do Hermes.