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

## Docker instalado posteriormente

Após a autorização autônoma, foram instalados e verificados:

- Docker Engine Ubuntu: `29.1.3-0ubuntu4.1`;
- Docker Compose V2: `2.40.3+ds1-0ubuntu1`;
- serviço Docker: ativo;
- cgroup: v2;
- diretório do daemon: `/var/lib/docker`.

O usuário `tlandskron` foi adicionado ao grupo `docker`, mas a sessão SSH atual pode precisar ser encerrada e reaberta para que a associação de grupo seja aplicada sem `sudo`.

Foi executado um teste controlado com `hello-world`; o digest observado foi:

```text
sha256:c567b231f65614688711a75f31518a064258a51d442117a3e771a3a8fdba78ef
```

Esse teste usa uma imagem de validação e não representa um pin de deployment do laboratório.

## Verificação final da manutenção

```text
qemu-guest-agent: active
swap: 8.0Gi total, 0B used
apt: no upgradable packages listed
docker: 29.1.3
docker compose: 2.40.3
```
