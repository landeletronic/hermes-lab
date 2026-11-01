# OpenSpec — critérios de teste

A matriz executável está em [`docs/test-checklist.md`](../../docs/test-checklist.md).

## Gates

- Baseline do host e containers.
- Isolamento de redes, volumes, bancos e credenciais.
- Saúde de Ollama, Honcho, Mem0 e Hermes.
- Persistência após reinício controlado.
- Backup e restauração em destino isolado.
- Latência, CPU, RAM, swap, disco e logs.
- Secret scan, imagem por digest e ausência de exposição indevida.

## Classificação

- `PASS`: evidência objetiva atende o critério.
- `FAIL`: critério violado ou erro sem explicação.
- `BLOCKED`: pré-requisito ausente ou operação requer autorização adicional.

Nenhum `BLOCKED` pode ser reportado como sucesso.
