# OpenSpec — segurança do host e exposição

## Firewall

- UFW instalado e habilitado.
- Política de entrada: negar por padrão.
- Política de saída: permitir por padrão.
- SSH permitido somente da rede administrativa LAN autorizada.
- Portas dos Webapps serão adicionadas somente quando os serviços estiverem prontos.
- PostgreSQL, Redis, Ollama e APIs internas não terão regras de entrada LAN.

## Requisitos de deployment

- Bind de portas Webapp restrito ao endereço LAN planejado, quando possível.
- Autenticação obrigatória antes da publicação.
- Redes Docker internas para dados e dependências.
- Sem `privileged`, sem `docker.sock` e sem capabilities desnecessárias.
- `no-new-privileges`, limites de memória/CPU/PIDs e rotação de logs.
- Segredos em arquivos locais com modo 600, fora do Git.

## Verificação

Registrar:

- `ufw status verbose`;
- `ss -lntup`;
- portas publicadas por `docker ps`;
- regras efetivas IPv4/IPv6;
- teste a partir de origem autorizada e não autorizada.

## Rollback

Antes de alterar regras, salvar o estado atual. Em caso de perda de acesso administrativo, restaurar as regras pelo console do hypervisor ou acesso out-of-band, nunca por credencial enviada no chat.
