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

## Resultado inicial

A primeira tentativa foi bloqueada porque o sudo exigia autenticação interativa. Depois que o acesso `sudo -n` foi disponibilizado para `tlandskron`, a manutenção foi concluída.

## Execução concluída

- `apt-get update` executado.
- `apt-get full-upgrade -y` executado com sucesso.
- 30 pacotes atualizados e 1 pacote novo instalado durante o upgrade.
- `qemu-guest-agent` instalado na versão `1:10.2.1+ds-1ubuntu3.3`.
- Serviço `qemu-guest-agent`: ativo.
- O serviço é uma unidade `static`; portanto `systemctl is-enabled` retorna `static`, o que é esperado para esse tipo de unidade.
- Nenhuma atualização pendente foi encontrada após a operação.
- Nenhum arquivo `/var/run/reboot-required` foi criado; não há reinicialização obrigatória indicada pelo sistema.

## Swap

O swap original era `/swap.img` com aproximadamente 3.8 GiB. A expansão inicial usando `truncate` produziu um arquivo esparso que o kernel rejeitou. O arquivo foi recriado corretamente usando `fallocate`:

- Caminho: `/swap.img`
- Tamanho: `8 GiB`
- Permissões: `600`
- Ativo: sim
- Uso no momento da verificação: `0 B`
- Entrada existente em `/etc/fstab`: `/swap.img none swap sw 0 0`

## Verificação final

```text
qemu-guest-agent: active
swap: 8.0Gi total, 0B used
apt: no upgradable packages listed
```
