# Manutenção do `hermes-lab` — 2026-11-01

## Solicitação

- Atualizar pacotes do Ubuntu.
- Instalar `qemu-guest-agent`.
- Expandir `/swap.img` de aproximadamente 3.8 GiB para 8 GiB.

## Verificação

A conexão SSH funciona com o usuário `tlandskron`, que pertence ao grupo `sudo`, mas o ambiente SSH não possui autorização sudo não interativa:

```text
sudo: interactive authentication is required
```

O arquivo `/swap.img` também não é gravável diretamente pelo usuário comum.

## Resultado

Nenhuma alteração privilegiada foi aplicada. O comando de atualização terminou antes de executar `apt-get update`, portanto não houve atualização de pacotes nem instalação do agente QEMU.

## Pré-requisito para continuar

É necessário executar as operações com uma destas alternativas:

1. disponibilizar sudo não interativo temporariamente para `tlandskron`; ou
2. executar manualmente os comandos privilegiados no host; ou
3. fornecer acesso SSH autorizado a uma conta administrativa/root, conforme a política do host.

Não registrar senhas no repositório nem enviá-las pelo chat.
