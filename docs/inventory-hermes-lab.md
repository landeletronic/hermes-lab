# Inventário do host `hermes-lab`

**Coleta inicial:** 2026-11-01 15:32:51 UTC
**Atualização de memória:** 2026-11-01 16:16:02 UTC
**Método:** comandos somente leitura via SSH
**Usuário remoto:** `tlandskron`

## Sistema operacional

- Hostname: `hermes-lab`
- Plataforma: máquina virtual KVM/QEMU
- Sistema: Ubuntu 26.04.1 LTS (`resolute`)
- Kernel: `7.0.0-42-generic`
- Arquitetura: `x86_64`
- Interface LAN: `ens18`
- IPv4 observado: `192.168.15.235/24`
- IPv6 global observado: `2804:7f4:c013:f9fb:be24:11ff:fe53:210d/64`

## Docker

- Docker CLI/Engine: **não instalado** ou não disponível no `PATH`.
- Docker Compose: **não instalado** ou não disponível no `PATH`.
- Serviço systemd `docker`: não encontrado/inativo.
- Containers existentes: não foi possível listar porque o comando `docker` não existe.

### Impacto

Antes de criar as stacks será necessário instalar Docker Engine e Docker Compose Plugin. A instalação deve ser documentada separadamente e validada antes de qualquer `docker compose up`.

## CPU

- CPUs lógicas: `4`
- Modelo: `QEMU Virtual CPU version 2.5+`
- Sockets: `1`
- Cores por socket: `4`
- Threads por core: `1`

## Memória

- RAM total: `7.2 GiB` observados pelo sistema (`7.5 GB` em `/proc/meminfo`)
- RAM disponível no momento da atualização: `6.7 GiB`
- RAM usada: `454 MiB`
- Swap total: `3.8 GiB`
- Swap usada: `0 B`

### Avaliação preliminar

A memória foi ampliada e agora oferece uma margem muito melhor para o laboratório. Ainda será necessário acompanhar o consumo durante a execução simultânea dos dois Hermes, Honcho, Mem0 e PostgreSQL, mas a capacidade atual é adequada para iniciar a implantação controlada. Devemos manter limites por container e monitoramento antes de aumentar a carga.

## Armazenamento

Partição raiz:

- Dispositivo: `/dev/mapper/ubuntu--vg-ubuntu--lv`
- Sistema de arquivos: `ext4`
- Tamanho: `500G`
- Usado: `6.7G`
- Disponível: `468G`
- Uso: `2%`
- Inodes usados: `1%`

Outros pontos relevantes:

- `/boot`: 2.0G, 8% usado.
- `/boot/efi`: 1.1G, 1% usado.
- `/tmp`: tmpfs de 1.7G.

Há espaço suficiente para o laboratório, imagens Docker, volumes e backups iniciais. A política de retenção de logs e backups ainda será definida.

## Firewall

- UFW: não instalado.
- firewalld: não instalado.

### Avaliação preliminar

Não foi possível confirmar uma política de firewall local por UFW ou firewalld. Antes de publicar Webapps na LAN, devemos definir explicitamente a política de filtragem e verificar também o firewall da camada de virtualização/rede.

## Portas TCP em escuta

| Endereço | Porta | Observação |
|---|---:|---|
| `0.0.0.0` / `[::]` | 22 | SSH |
| `127.0.0.53` / `127.0.0.54` | 53 | resolvedor DNS local |

Não foram observadas portas TCP de Docker, Hermes, Honcho, Mem0 ou PostgreSQL em escuta no momento da coleta.

As portas dos dois Webapps ainda não foram reservadas. Sugestão inicial, sujeita à validação:

- Hermes + Honcho: `9121`
- Hermes + Mem0: `9122`

Os PostgreSQL devem ficar apenas na rede interna do Docker, sem publicação na interface LAN, salvo necessidade operacional documentada.

## Pré-requisitos antes dos containers

- [ ] Instalar Docker Engine pelo repositório oficial apropriado para Ubuntu.
- [ ] Instalar Docker Compose Plugin.
- [ ] Validar `docker run hello-world` ou equivalente.
- [ ] Definir usuário/grupo e política de acesso ao Docker socket.
- [ ] Definir firewall para SSH e Webapps.
- [ ] Confirmar se 3.3 GiB de RAM será suficiente ou aumentar a RAM da VM.
- [ ] Definir diretórios de volumes fora do checkout Git.
- [ ] Definir política de backup e retenção.
- [ ] Confirmar requisitos de PostgreSQL, Honcho e Mem0.
- [ ] Fixar versões das imagens antes de criar os Compose.

## Comandos executados

O inventário foi coletado com comandos de leitura para:

- identidade do host e sistema operacional;
- versão e estado do Docker/Compose;
- CPU e memória;
- sistemas de arquivos e inodes;
- interfaces de rede;
- serviços de firewall;
- portas TCP/UDP em escuta;
- estado de pacotes e serviço systemd do Docker.
