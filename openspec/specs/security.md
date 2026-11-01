# OpenSpec — Segurança

## Requisitos

- [ ] Webapps protegidos por autenticação.
- [ ] Webapps limitados à LAN/VPN autorizada.
- [ ] Bancos e APIs de memória somente em redes Docker internas.
- [ ] Segredos em arquivos locais com modo 600 ou secret manager.
- [ ] Imagens fixadas por versão e digest.
- [ ] `cap_drop: [ALL]` e `no-new-privileges` quando compatíveis.
- [ ] Limites de CPU, memória, PIDs e logs.
- [ ] Nenhum `/var/run/docker.sock` montado.
- [ ] Firewall explícito para SSH e Webapps.
- [ ] Gitleaks/TruffleHog executado antes de cada publicação.
- [ ] Backups criptografados e restauração testada.

## Dados públicos

Inventários públicos devem omitir hostname real, endereços IP, interfaces, regras detalhadas, portas administrativas e identificadores do host. Esses dados devem existir somente em documentação operacional privada.
