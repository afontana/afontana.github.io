+++
title = "Estado atual do blueprint: requisitos, o que foi feito, problemas corrigidos e próximos passos"
date = 2026-07-23

[taxonomies]
tags = ["arquitetura", "requisitos", "gestão de projetos"]
+++

Este é um resumo do estado atual do blueprint do laboratório: a
versão viva do documento que venho mantendo, com cada requisito, cada
correção e cada pendência registrada com prioridade explícita, não
como lista solta.

## Definições fundamentais

O laboratório é organizado em quatro perfis de trabalho isolados,
sobre uma base imutável com snapshots e atualização transacional. Três
perfis rodam containerizados; o perfil de análise de malware roda
inteiro em máquina virtual, isolamento proporcional ao risco de cada
contexto. Um dos perfis já implementa o modelo completo de workspace
por fase, com toolset curado e editável por fase, e serve de padrão de
referência para replicar nos demais.

## Requisitos funcionais

| ID | Requisito | Status |
|----|-----------|--------|
| RF-TOOL-03 | Navegador de uso geral, isolado por perfil | ✅ Implementado |
| RF-TOOL-03c | Ferramentas gráficas adicionais, sob demanda, isoladas por perfil | ✅ Implementado |
| RF-TOOL-05 | Gerenciamento consolidado de recursos, com rótulo de segurança de remoção | ✅ Implementado |
| RF-TOOL-06 | Curadoria de toolset editável sem tocar em sintaxe de baixo nível | ✅ Implementado |
| RF-TOOL-07 | Validação preventiva de pacotes antes de qualquer build | ✅ Implementado |
| RF-UI-01 | Workspaces nomeados por perfil, por fase | 🔄 Completo em um perfil, pendente replicar nos demais |
| RF-UI-04 | Painel de observabilidade do sistema em uso | ✅ Implementado, simplificado nesta versão |

## Requisitos não funcionais

| ID | Requisito | Status |
|----|-----------|--------|
| RNF-PERF-05 | Renderização gráfica estável sob virtualização aninhada | ✅ Implementado, validado extensivamente |
| RNF-PERF-06 | Memória suficiente sob carga de múltiplos containers simultâneos | ✅ Implementado, corrigido após incidente real |
| RNF-SEC-09 | Isolamento de instalação de ferramenta por perfil, sem vazamento entre contextos | ✅ Implementado, revisado após violação identificada em produção |
| RNF-MAINT-05 | Runtime de container compatível com operações completas de sistema | ❓ Dívida técnica registrada, sem solução nativa disponível ainda |

## O que já foi feito

A ferramenta de linha de comando que gerencia o laboratório está
implementada e validada extensivamente num dos perfis: cria, entra,
edita e reconstrói o ambiente de cada fase sob demanda, sem
persistência forçada, com recriação automática se algo for apagado. A
validação de pacotes roda antes de qualquer construção de ambiente,
com sugestão de alternativa quando algo falha. Um painel de
administração central dá visão de tudo que está em uso, com rótulo de
segurança indicando o que é seguro remover.

A arquitetura de isolamento de ferramentas gráficas passou por três
tentativas diferentes antes de chegar na solução atual, cada reversão
motivada por uma causa técnica real, não indecisão: a primeira
abordagem expôs um bug de plataforma; a segunda, mais simples, violava
um requisito de isolamento já registrado; a terceira resolveu as duas
questões ao mesmo tempo, usando um mecanismo de sandbox que não
depende do runtime de container que estava com problema.

## Problemas corrigidos

**Esgotamento de memória sob carga.** Múltiplos ambientes ativos
simultaneamente, sem serem encerrados após uso, geraram erro de
alocação de memória durante uma instalação. A correção não foi só
técnica (memória de troca adicionada): o incidente motivou a criação
do próprio painel de administração central, para dar visibilidade
contra esse tipo de acúmulo silencioso antes que volte a causar
problema.

**Vazamento de isolamento entre perfis.** Uma decisão que parecia mais
simples, instalar uma ferramenta gráfica direto no sistema em vez de
isolada por perfil, foi revertida ao ficar claro que violava o
requisito de isolamento por perfil. A ferramenta afetada foi migrada
para o mecanismo de sandbox isolado.

**Autenticação inútil dentro do ambiente isolado.** Um usuário
fantasma, sem senha real, tornava um mecanismo de elevação de
privilégio inutilizável dentro do ambiente. Corrigido usando acesso
direto como usuário privilegiado dentro do container, já que o
isolamento real está na fronteira do ambiente, não numa segunda camada
de autenticação sem propósito dentro dele.

**Bug de plataforma em runtime de container.** Um erro persistente ao
instalar determinadas ferramentas foi inicialmente suspeitado como
falta de memória, parcialmente verdade, mas a causa raiz real era uma
limitação do runtime de container ativo, sem correção nativa
disponível no sistema base. Registrado como dívida técnica e
contornado pela migração para o mecanismo de sandbox.

## Próximos passos

- Replicar a estrutura completa de workspace por fase (curadoria,
  validação, administração) para os perfis que ainda estão no formato
  antigo.
- Definir se o projeto é de uso individual ou multiusuário, decisão
  que afeta a criticidade de várias escolhas de isolamento já
  tomadas.
- Investigar uma imagem de ambiente com tamanho fora do padrão
  esperado em relação às demais, apesar de lista de pacotes
  comparável. Não bloqueante, mas fora do esperado.
- Avaliar um mecanismo de ambiente especial para ferramentas que
  exigem acesso direto a dispositivo do host, com documentação
  explícita de cada permissão concedida e por quê.
- Tornar o indicador de fase na interface dinâmico, baseado na
  existência real do ambiente correspondente ao workspace atual, sem
  lista fixa de nomes.
- Exibir o comando exato antes de executá-lo em qualquer operação de
  ambiente, com valor educacional e reforço de aviso em contextos de
  isolamento reduzido.
- Avaliar a adoção de uma solução robusta de anonimato de rede,
  disponível como recurso transversal a partir de qualquer perfil.
- Formalizar licença, decisões de arquitetura documentadas, e modelo
  de ameaça completo.

Nenhum desses itens está escondido. Cada um está registrado com
prioridade e estado explícitos, e é essa disciplina, não a lista em
si, que é o assunto real desta série.
