# Aplicativos de programação com IA

> **Objetivo deste documento**
>
> Apresentar, de forma introdutória, algumas ferramentas que utilizam inteligência artificial para ajudar no desenvolvimento de software.
>
> A tabela não estabelece um ranking. Cada aplicação possui uma proposta, um nível de automação e um modelo de distribuição diferente.

## 1. Antes de começar: o que essas ferramentas fazem?

Um aplicativo de programação com IA pode ajudar a:

- explicar um código existente;
- criar novos arquivos e funções;
- alterar vários arquivos de um projeto;
- executar comandos no terminal;
- rodar testes e analisar os resultados;
- sugerir correções para erros;
- revisar alterações antes de serem aplicadas;
- criar pull requests ou tarefas de desenvolvimento;
- automatizar partes do trabalho de desenvolvimento.

Algumas ferramentas funcionam dentro de uma **IDE**, como o VS Code ou os IDEs da JetBrains. Outras funcionam no **terminal**. Há também ferramentas que executam tarefas em ambientes de nuvem.

### Termos importantes

| Termo | Explicação simples |
|---|---|
| **IDE** | Programa usado para escrever e organizar código, como VS Code, IntelliJ IDEA ou PyCharm. |
| **Terminal** | Interface de texto usada para executar comandos no computador. |
| **Agente** | IA que pode planejar e executar várias etapas, em vez de apenas responder a uma pergunta. |
| **Open source** | Software cujo código-fonte pode ser estudado, modificado e, conforme a licença, redistribuído. |
| **Modelo de IA** | O modelo linguístico usado para entender instruções e gerar respostas ou código. |
| **MCP** | Padrão que permite conectar modelos de IA a ferramentas e fontes de dados externas. |
| **Pull request** | Solicitação para revisar e incorporar alterações em um repositório de código. |

## 2. Quadro comparativo

A coluna **Tipo** indica, de maneira geral, como o produto é distribuído. Ela não representa necessariamente o modelo de cobrança.

| Aplicação | Fabricante / mantenedor | Tipo | Em termos simples |
|---|---|---|---|
| **Codex** | OpenAI | Proprietário | Agente para desenvolver, editar arquivos, executar comandos, rodar testes e trabalhar localmente ou na nuvem. |
| **Claude Code** | Anthropic | Proprietário | Agente de terminal que trabalha diretamente em repositórios, executa comandos e modifica arquivos. Também pode ser usado com VS Code e JetBrains. ([documentação](https://docs.anthropic.com/en/docs/claude-code/getting-started)) |
| **OpenCode** | Anomaly Innovations / comunidade SST | Open source | Agente independente de fornecedor, com suporte a vários provedores, modelos locais, terminal, desktop e integrações com IDEs. ([site oficial](https://open-code.software/)) |
| **Qoder** | Alibaba Cloud | Proprietário | Plataforma com IDE, CLI, integração com JetBrains, planejamento, revisão e automação de tarefas. ([documentação](https://www.alibabacloud.com/help/en/model-studio/qoder-agent)) |
| **Hermes Agent** | Nous Research | Open source, licença MIT | Agente geral extensível, com terminal, ferramentas, memória persistente, skills, plugins, MCP, subagentes, automações, navegador, voz e integrações. |
| **Cursor** | Anysphere | Proprietário | IDE baseada no VS Code com recursos de IA para pesquisar o projeto, editar vários arquivos e executar comandos. ([documentação](https://docs.cursor.com/chat/overview)) |
| **GitHub Copilot** | GitHub / Microsoft | Proprietário | Assistente integrado ao GitHub e a vários IDEs, com geração de código, chat, revisão e modos agentic. ([agentes](https://github.com/features/copilot/agents)) |
| **Gemini CLI** | Google | Open source, Apache 2.0 | Agente de terminal baseado no Gemini, com ferramentas, MCP e instruções específicas para cada projeto. ([repositório](https://github.com/google-gemini/gemini-cli)) |
| **Antigravity CLI** | Google | Proprietário / ecossistema Google | Ferramenta agentic do Google voltada ao desenvolvimento interativo no terminal e ao uso dos modelos Gemini. |
| **Cline** | Cline | Open source | Agente para VS Code, JetBrains e desktop. Permite escolher o provedor e o modelo de IA. |
| **Roo Code** | Roo Code | Open source | Agente derivado do ecossistema Cline, com modos especializados, subagentes e personalização. |
| **Aider** | Aider AI / comunidade | Open source, Apache 2.0 | Agente de terminal centrado em Git, edição de repositórios, diffs e revisão das alterações. |
| **Goose** | Block | Open source | Agente para desenvolvimento, automação, execução de ferramentas e integrações via MCP. |
| **OpenHands** | All Hands AI | Open source | Agente autônomo para desenvolvimento, com ambiente isolado e suporte a tarefas longas. |
| **Kiro** | AWS | Proprietário | IDE agentic baseada em Code OSS, com desenvolvimento orientado por especificações, requisitos, tarefas e testes. |
| **Amazon Q Developer** | Amazon Web Services | Proprietário | Assistente e agente para desenvolvimento, com integração especialmente forte com AWS e modernização de aplicações. ([documentação](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html)) |
| **Devin** | Cognition | Proprietário | Agente de engenharia de software hospedado na nuvem, voltado para tickets, implementação, testes e pull requests. |
| **Windsurf / Devin Desktop** | Cognition | Proprietário | IDE agentic para desenvolvimento interativo, integrada ao ecossistema da Cognition/Devin. |
| **JetBrains Junie** | JetBrains | Proprietário | Agente integrado a IDEs como IntelliJ IDEA, PyCharm e WebStorm. |
| **Zed AI** | Zed Industries | Open source / parcialmente proprietário | Editor de código de alto desempenho com recursos agentic e suporte a diferentes provedores. |
| **Mistral Vibe** | Mistral AI | Proprietário | Agente de programação para terminal e IDE baseado nos modelos da Mistral. |
| **Qwen Code** | Alibaba / Qwen | Open source | Agente de terminal voltado ao ecossistema Qwen e a APIs compatíveis. |
| **Kimi Code** | Moonshot AI | Proprietário | Agente de terminal e IDE baseado nos modelos Kimi. |
| **MiniMax Code** | MiniMax | Open source / modelos proprietários | Agente de terminal integrado ao ecossistema de modelos MiniMax. |
| **Tabnine** | Tabnine | Proprietário | Assistente corporativo para completions, chat, geração de código e controles de privacidade. |
| **Sourcegraph Cody** | Sourcegraph | Proprietário | Assistente para compreender grandes bases de código, realizar busca semântica e gerar alterações. |
| **Qodo** | Qodo | Proprietário | Ferramenta focada em testes, revisão de código, qualidade e pull requests. |
| **Replit Agent** | Replit | Proprietário | Agente integrado ao Replit para criar aplicações no navegador, executar código e realizar deploy. |

## 3. Como as ferramentas podem ser agrupadas?

O agrupamento abaixo serve apenas para facilitar o entendimento. Uma mesma aplicação pode aparecer em mais de uma categoria.

| Grupo | Exemplos | O que isso significa |
|---|---|---|
| **Uso no terminal** | Codex, Claude Code, OpenCode, Hermes Agent, Gemini CLI, Aider e Goose | A pessoa interage com a ferramenta por comandos e instruções de texto. |
| **Uso dentro de uma IDE** | Cursor, Qoder, Kiro, Windsurf, Zed e JetBrains Junie | A IA fica integrada ao programa usado para escrever código. |
| **Código aberto** | Hermes Agent, OpenCode, Gemini CLI, Cline, Roo Code, Aider, Goose, OpenHands e Qwen Code | O projeto disponibiliza seu código-fonte, respeitando os termos da licença correspondente. |
| **Tarefas mais autônomas** | Hermes Agent, OpenHands, Devin, Codex, Claude Code e Goose | A ferramenta pode planejar e executar várias etapas de uma tarefa. |
| **Automação e integração** | Hermes Agent, Goose e OpenHands | A ferramenta pode conectar modelos de IA a comandos, serviços e outras ferramentas. |
| **Integração com GitHub** | GitHub Copilot, Codex, Claude Code, Cursor e OpenHands | A ferramenta pode ajudar com repositórios, alterações, revisões ou pull requests. |
| **Integração com AWS** | Amazon Q Developer e Kiro | A ferramenta possui integração com serviços e fluxos da AWS. |
| **Múltiplos provedores ou modelos locais** | Hermes Agent, OpenCode, Aider, Cline, Roo Code, Goose e OpenHands | A ferramenta pode trabalhar com mais de um provedor ou com modelos executados pelo próprio usuário. |
| **Testes e qualidade** | Qodo, GitHub Copilot, Claude Code, Cursor e Hermes Agent | A ferramenta pode ajudar a criar testes, revisar código e identificar problemas. |
| **Criação rápida de aplicações web** | Replit Agent, Cursor, Qoder e Devin Desktop | A ferramenta pode auxiliar na criação e publicação de aplicações. |

## 4. O que observar ao escolher uma ferramenta?

Para uma pessoa que está começando, alguns critérios são mais importantes do que uma lista de recursos:

1. **Onde a ferramenta funciona?** No terminal, no VS Code, em outro IDE ou no navegador?
2. **Ela pode alterar arquivos automaticamente?** Algumas ferramentas apenas sugerem código; outras podem executar mudanças.
3. **Ela pode executar comandos?** Isso é útil, mas exige atenção, principalmente em projetos reais.
4. **Qual modelo de IA ela utiliza?** A qualidade e o custo podem variar conforme o modelo.
5. **É necessário fornecer uma chave de API?** Algumas aplicações incluem o acesso ao modelo; outras dependem da conta do usuário.
6. **O código é enviado para um serviço externo?** Esse ponto é importante para projetos privados ou corporativos.
7. **Existe revisão antes da execução?** É recomendável conferir comandos e alterações antes de aplicá-los.
8. **A licença é adequada ao uso pretendido?** Código aberto não significa que todas as formas de uso são iguais.
9. **A ferramenta mantém memória ou contexto do projeto?** Isso pode facilitar tarefas longas, mas também aumenta a importância de controlar quais informações são compartilhadas.

> **Atenção:** uma ferramenta de programação com IA não substitui a revisão humana. Antes de aceitar alterações, confira o diff, execute os testes e confirme se o comando não modifica ou remove dados importantes.

## 5. Observações sobre o Hermes Agent

O **Hermes Agent**, da **Nous Research**, pode ser incluído tanto entre as ferramentas de programação quanto entre as plataformas gerais de agentes de IA.

Ele não se limita a completar código ou responder perguntas. Dependendo da configuração, pode:

- trabalhar no terminal;
- executar ferramentas e comandos;
- editar arquivos;
- usar memória persistente;
- carregar skills e plugins;
- conectar-se a ferramentas por MCP;
- delegar tarefas para subagentes;
- executar automações agendadas;
- interagir com navegador;
- trabalhar com voz e recursos multimodais;
- integrar-se a editores compatíveis com ACP.

Por isso, o Hermes Agent é mais amplo do que um assistente de código tradicional. Ele pode ser usado como agente de desenvolvimento, mas também como plataforma de automação e agente pessoal extensível.

O projeto é open source e distribuído sob a licença MIT. O **Nous Portal**, por outro lado, é um serviço separado da Nous Research que oferece modelos e ferramentas hospedadas.

## 6. Observações sobre o OpenCode

O **OpenCode não é um produto da OpenAI**. Apesar de poder utilizar modelos da OpenAI, Anthropic, Google, modelos locais e outros provedores, o projeto é independente do fabricante desses modelos.

O OpenCode é associado à **Anomaly Innovations** e à comunidade relacionada ao SST. Sua proposta é oferecer um agente de programação independente de fornecedor, com suporte a diferentes modelos e provedores.

Essa separação é importante:

- **OpenCode** é a aplicação ou agente usado para trabalhar no projeto;
- **OpenAI, Anthropic, Google e outros** são possíveis fornecedores dos modelos de IA utilizados pelo agente;
- o usuário pode escolher a combinação de aplicação e modelo conforme disponibilidade, custo, privacidade e capacidade técnica.

## 7. Fontes e referências

- [OpenAI Codex](https://academy.openai.com/public/clubs/builders-etkn1/resources/codex-for-builders)
- [Anthropic Claude Code](https://docs.anthropic.com/en/docs/claude-code/getting-started)
- [OpenCode](https://open-code.software/)
- [Alibaba Cloud Qoder](https://www.alibabacloud.com/help/en/model-studio/qoder-agent)
- [Cursor](https://docs.cursor.com/chat/overview)
- [GitHub Copilot Agents](https://github.com/features/copilot/agents)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [Amazon Q Developer](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/what-is.html)
- [Hermes Agent — visão geral de recursos](https://hermes-agent.nousresearch.com/docs/user-guide/features/overview)
- [Hermes Agent — perguntas frequentes](https://hermes-agent.nousresearch.com/docs/reference/faq)
