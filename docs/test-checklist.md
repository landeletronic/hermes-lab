# Matriz de testes e critérios de aceitação
## Ollama + Honcho + Mem0 + Hermes

**Escopo:** validação somente leitura de uma implantação Docker/Compose em um host Linux de laboratório. Não instalar, reiniciar, alterar firewall, publicar portas, criar volumes ou modificar configurações durante esta execução.

**Regra de evidência:** cada teste deve guardar comando, data/hora UTC, código de saída, stdout/stderr e artefatos referenciados. Substitua `<project>` e `<container>` pelos nomes reais descobertos na inspeção.

```bash
export EVID=/var/tmp/ollama-stack-test-$(date -u +%Y%m%dT%H%M%SZ)
mkdir -p "$EVID"
exec > >(tee "$EVID/run.log") 2>&1
set -o pipefail
printf 'started_utc=%s\n' "$(date -u +%FT%TZ)"
```

## Critérios globais de aprovação

- **PASS:** evidência objetiva atende integralmente ao critério, sem erro não explicado.
- **FAIL:** qualquer exposição indevida, mistura de estado, perda de dados, restauração não verificável, erro de autenticação, latência acima do orçamento ou consumo sem limite.
- **BLOCKED:** pré-requisito ausente; não converter em PASS.
- Nenhum teste deve alterar o host. Testes destrutivos de backup/restore devem ocorrer somente em cópia/ambiente isolado e explicitamente autorizado; nesta etapa, validar apenas existência, legibilidade e restauração em destino temporário não produtivo.

## 0. Descoberta e baseline — obrigatório

| ID | Verificação executável | Evidência esperada | Aceitação |
|---|---|---|---|
| B-01 | `hostnamectl; uname -a; nproc; free -h; swapon --show; df -hT; docker version; docker compose version` | Saída versionada no log | Host, kernel, CPU, RAM/swap, discos, Docker e Compose identificados; discrepâncias registradas |
| B-02 | `docker compose ls; docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}'` | Lista de projetos/containers | Ollama, Honcho, Mem0, Hermes, bancos e dependências identificados; imagens e tags anotadas |
| B-03 | `docker inspect $(docker ps -q) --format '{{.Name}} {{.Config.Image}} {{.HostConfig.ReadonlyRootfs}} {{.HostConfig.Memory}} {{.HostConfig.NanoCpus}}'` | Limites por container | Serviços têm limites explícitos ou exceção documentada; rootfs somente leitura quando compatível |
| B-04 | `ss -lntup; docker ps --format '{{.Names}} {{.Ports}}'` | Sockets e bindings | Somente Webapps necessários publicados; Ollama, Honcho, Mem0 e bancos não escutam em `0.0.0.0`/`::` sem justificativa |
| B-05 | `docker network ls; docker volume ls` | Inventário de redes/volumes | Redes e volumes do projeto nomeados; nenhum volume compartilhado acidentalmente |
| B-06 | `docker system df -v` | Uso de imagens, containers e volumes | Capacidade disponível suficiente para modelo, bancos, logs e backup; margem mínima definida no plano |

## 1. Isolamento de redes

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| NET-01 | `docker network inspect <network>` | Containers, subnet, gateway, `Internal`, aliases | Rede de dados separada da rede de entrada; somente pares necessários conectados |
| NET-02 | Para cada container: `docker inspect <container> --format '{{json .NetworkSettings.Networks}}'` | Mapa container→rede | Nenhum banco ou serviço interno conectado à rede pública/LAN sem requisito aprovado |
| NET-03 | Em container de teste já existente, usar somente ferramentas presentes: `getent hosts <service>` e `nc -zvw2 <service> <port>` | Matriz de conectividade permitida/negada | Conectividade permitida funciona; caminhos não permitidos falham ou não resolvem |
| NET-04 | A partir de host autorizado e origem não autorizada, testar apenas endpoints publicados com `curl -sk -o /dev/null -w '%{http_code} %{remote_ip}\n' <url>` | HTTP status, IP remoto, timestamp | Webapps acessíveis somente pela LAN/VPN autorizada; origem externa bloqueada |
| NET-05 | `docker inspect <container> --format '{{json .Config.Env}}'` e revisão do Compose | Não há URLs/credenciais de outro stack | Cada stack usa redes, nomes DNS, credenciais e bancos próprios |

## 2. Isolamento de volumes, bancos e dados

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| ISO-01 | `docker inspect <container> --format '{{range .Mounts}}{{.Source}} -> {{.Destination}} ({{.RW}}){{"\\n"}}{{end}}'` | Mapa de mounts e modo RW/RO | Modelo Ollama, dados Honcho, Mem0, Hermes, bancos, logs e backups em volumes distintos |
| ISO-02 | `docker volume inspect <volume>` para todos os volumes | Nome, mountpoint, labels | Volumes têm labels do projeto/ambiente; nenhum volume de produção montado em teste |
| ISO-03 | Revisar Compose para `POSTGRES_DB`, usuário, schema e host | Configuração dos bancos | Bancos separados por serviço/stack; credenciais e schemas não reutilizados sem justificativa |
| ISO-04 | Consultar somente metadados do banco com cliente já instalado: versão, databases, schemas e tamanho | Saída sem dados sensíveis | Mem0/Honcho/Hermes apontam para o banco correto; tabelas/índices esperados presentes |
| ISO-05 | Criar marcador somente em ambiente descartável autorizado; em produção, apenas comparar identificadores de volume | Marcador aparece exclusivamente no destino previsto | Leitura cruzada entre stacks não ocorre; se não houver ambiente descartável, marcar BLOCKED |
| ISO-06 | `docker diff <container>` após operação normal | Lista de alterações | Nenhum segredo, banco ou embedding gravado em camada efêmera quando deveria ser persistente |

## 3. Saúde funcional e fluxo ponta a ponta

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| FUNC-01 | `docker inspect <container> --format '{{.State.Status}} {{.State.Health.Status}} {{.State.ExitCode}}'` | Estado/health de todos | Todos os serviços necessários `running` e `healthy`, ou healthcheck equivalente documentado |
| FUNC-02 | Chamadas autenticadas aos endpoints de saúde/modelo já documentados | Status, corpo sanitizado, duração | Ollama responde, modelo selecionado existe, Honcho/Mem0 respondem e Hermes conclui bootstrap |
| FUNC-03 | Fluxo: sessão Hermes→Ollama; gravação/extração em Honcho/Mem0; segunda consulta recupera memória | IDs de correlação e resposta | Memória criada é recuperável na mesma sessão e após nova sessão, sem vazamento entre usuários |
| FUNC-04 | Repetir com dois usuários/tenants e marcadores distintos | Matriz usuário→memória recuperada | Cada usuário recupera somente seu escopo; ausência de marcador cruzado |
| FUNC-05 | Logs correlacionados por request/session ID | Trechos de log sanitizados | Erros têm causa identificável; tokens, prompts privados e dados pessoais não aparecem em claro |

## 4. Persistência e reinício controlado

> Executar reinício somente em janela autorizada. Sem autorização, coletar configuração e marcar os casos como BLOCKED.

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| PERSIST-01 | Antes do reinício, registrar checksum lógico/contagem de registros, lista de modelos e marcador de teste | Baseline reprodutível | Baseline salvo em `$EVID`, sem conteúdo sensível |
| PERSIST-02 | Reiniciar apenas um serviço por vez (`docker compose restart <service>`) | Evento, duração, health pós-restart | Serviço retorna ao saudável dentro do RTO definido; sem perda do marcador |
| PERSIST-03 | Reiniciar projeto completo em janela autorizada | Estado antes/depois | Todos os dados, modelos e configurações persistem; dependências sobem em ordem válida |
| PERSIST-04 | `docker compose down`/`up` somente em clone descartável | Comparação pré/pós | Volumes persistentes preservam estado; volumes efêmeros são explicitamente aceitos |
| PERSIST-05 | Verificar permissões/ownership dos mounts | `stat`, `find` limitado e sanitizado | Serviço acessa somente seu volume; sem permissões world-writable |

## 5. Backup e restore

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| BR-01 | Inventariar jobs/scripts/política: `find <backup-path> -maxdepth 2 -type f -printf '%TY-%Tm-%Td %TH:%TM %s %p\\n'` | Lista, data, tamanho e retenção | Backup de bancos, Mem0/Honcho, configuração, Hermes e modelos conforme RPO |
| BR-02 | Verificar integridade sem alterar origem: `sha256sum <backup-files>` e ferramenta nativa de archive/database | Checksums e resultado de teste | Arquivos legíveis, checksum armazenado fora do conjunto principal e sem segredo exposto |
| BR-03 | Confirmar criptografia e controle de acesso: `stat -c '%a %U:%G %n' <backup-files>`; inspecionar apenas metadados | Permissões e tipo de armazenamento | Backups protegidos; chaves fora do backup; nenhum arquivo público |
| BR-04 | Restaurar em projeto/volume temporário isolado, nunca em produção | Log de restore, volumes e health | Restore completo, reproduzível e sem colisão de nomes/rede/banco |
| BR-05 | Comparar marcador, contagens, checksums lógicos e consulta ponta a ponta pós-restore | Tabela antes→depois | Dados críticos equivalentes; diferenças explicadas; RTO/RPO medidos e dentro do objetivo |
| BR-06 | Testar backup incompleto/corrompido em clone | Falha controlada e mensagem clara | Processo rejeita backup inválido sem sobrescrever destino íntegro |

## 6. Latência e desempenho

Defina antes do teste: `N` requisições, tamanho do prompt, modelo, temperatura, concorrência, hardware, cache quente/frio e orçamento. Não comparar números obtidos com condições diferentes.

| ID | Teste | Evidência esperada | Critério de aceitação sugerido |
|---|---|---|---|
| PERF-01 | Medir endpoint de saúde: `curl -sS -o /dev/null -w 'code=%{http_code} dns=%{time_namelookup} connect=%{time_connect} ttfb=%{time_starttransfer} total=%{time_total}\\n' <url>` repetido | p50/p95/p99, erro e amostras | p95 dentro do SLO definido; erros zero ou abaixo do limite aprovado |
| PERF-02 | Medir inferência curta, média e longa com payload fixo | Tempo total, tokens/s se disponível, modelo/cache | p95 por classe de prompt dentro do orçamento; outliers explicados |
| PERF-03 | Medir fluxo completo Hermes→Ollama→memória→resposta | Latência por etapa e total | Dependência dominante identificada; total dentro do SLO de produto |
| PERF-04 | Concorrência baixa/média no clone ou janela autorizada | Throughput, p95, fila, erros | Sem OOM, timeouts ou degradação além do limite; backpressure observável |
| PERF-05 | Medir cold start após descarregar/reiniciar somente em ambiente autorizado | Tempo até health e primeira resposta | Cold start dentro do RTO; modelo não é recarregado desnecessariamente em requisições quentes |

Exemplo de coleta sem ferramenta adicional:

```bash
for i in $(seq 1 20); do
  date -u +%FT%TZ
  curl -sS -o /dev/null -w 'code=%{http_code} total=%{time_total}\n' <health-url> || true
done | tee "$EVID/latency-health.txt"
```

## 7. Consumo e limites

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| RES-01 | `docker stats --no-stream --format '{{.Name}} {{.CPUPerc}} {{.MemUsage}} {{.MemPerc}} {{.NetIO}} {{.BlockIO}}'` em baseline e carga | Amostras por serviço | Nenhum serviço excede limite; memória total deixa margem operacional |
| RES-02 | `docker inspect` para `Memory`, `NanoCpus`, `PidsLimit`, `Ulimits` | Limites efetivos | Limites definidos para todos; serviços críticos não podem consumir host indefinidamente |
| RES-03 | `free -h; vmstat 1 5; df -hT; df -ih` | RAM, swap, I/O, espaço e inodes | Sem swap thrash, OOM, saturação de inode ou disco durante carga |
| RES-04 | `docker system df -v` antes/depois de N inferências | Crescimento de imagens, logs e volumes | Crescimento explicado; retenção/rotação de logs funcionando |
| RES-05 | Procurar erros de pressão: `docker events --since 10m` e `journalctl -k --since '-10 min'` (somente leitura) | OOMKilled, throttling, I/O, kernel warnings | Nenhum evento crítico não explicado |
| RES-06 | Verificar logging/rotação no Compose e Docker | Driver, max-size/max-file ou equivalente | Logs não podem consumir disco sem limite |

## 8. Segurança operacional e dados

| ID | Teste | Evidência esperada | Critério de aceitação |
|---|---|---|---|
| SEC-01 | Revisar env/config montados sem imprimir valores: listar somente nomes de variáveis e permissões | Inventário sanitizado | Segredos não aparecem em imagem, Git, logs ou `docker inspect` público |
| SEC-02 | `docker inspect` de usuário, capabilities e privilégios | User, CapAdd/Drop, privileged | Sem `privileged: true` ou capabilities desnecessárias; processo não-root quando suportado |
| SEC-03 | Confirmar bind address, firewall e exposição IPv4/IPv6 | Sockets e regra efetiva | LAN/VPN-only comprovado de origem autorizada e não autorizada |
| SEC-04 | Verificar retenção/eliminação de prompts, memórias, embeddings e backups | Política e amostra de metadados | Retenção aprovada, exclusão testável e sem cópias órfãs |
| SEC-05 | Revisar imagens por digest, origem e CVE scan já aprovado | SBOM/scan e digest | Imagens fixadas; vulnerabilidades críticas tratadas ou exceção formal |

## 9. Relatório mínimo de evidências

Produzir `test-report.md` contendo:

1. Identificação do ambiente, commit/arquivo Compose e data/hora UTC.
2. Tabela de versões e digests de imagens.
3. Diagrama textual de redes, volumes, bancos e portas.
4. Resultados por ID: `PASS`, `FAIL` ou `BLOCKED`, comando, evidência e observação.
5. Métricas: p50/p95/p99, throughput, erro, CPU, RAM, swap, I/O, disco e crescimento de logs.
6. RPO/RTO observado e resultado do restore.
7. Lista de riscos residuais, owner e prazo.
8. Hash dos artefatos de evidência; remover prompts, tokens, PII e dumps antes de compartilhar.

## Gate de aceite final

A implantação só é aceita quando:

- `B-*`, `NET-*`, `ISO-*`, `FUNC-*`, `PERSIST-*`, `BR-*`, `PERF-*`, `RES-*` e `SEC-*` obrigatórios estão `PASS` ou têm exceção formal aprovada.
- Não existe exposição indevida de banco, Ollama, Honcho ou Mem0.
- Não existe volume, rede, credencial ou banco compartilhado acidentalmente entre stacks.
- Um restore em ambiente isolado recupera o fluxo ponta a ponta e atende RPO/RTO.
- Latência e consumo foram medidos sob condições documentadas e permanecem dentro dos SLOs.
- Não houve alteração no host durante a execução; qualquer teste que exigia restart/destruição foi marcado `BLOCKED` quando não autorizado.
