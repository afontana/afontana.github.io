+++
title = "Sem gestão de projeto, um blueprint é só uma lista de desejos"
date = 2026-07-23
description = "Requisito sem prioridade não orienta decisão nenhuma. É isso que a disciplina de gestão resolve."

[taxonomies]
tags = ["gestão de projetos", "arquitetura", "requisitos"]
+++

Qualquer engenheiro consegue listar o que um sistema deveria fazer. O
difícil não é gerar requisito. É decidir qual importa mais quando dois
entram em conflito, e ter isso registrado de um jeito que sobrevive ao
tempo. É aí que gestão de projeto para de ser burocracia e vira
ferramenta de raciocínio.

Tenho mais de 20 anos trabalhando com isso, majoritariamente em
metodologias tradicionais. Quando comecei a desenhar o blueprint do
laboratório de segurança que venho construindo, apliquei o mesmo rigor
que aplicaria a qualquer projeto de infraestrutura crítica. O
resultado surpreendeu até a mim: a disciplina que a gestão de projeto
oferece não é um acessório de organização. É o que torna o blueprint
capaz de evoluir sem se contradizer.

## Funcional e não funcional não competem, mas raramente são tratados juntos

Um requisito funcional descreve o que o sistema faz. Um não funcional
descreve as condições sob as quais isso precisa acontecer, isolamento,
desempenho, segurança, manutenibilidade. Documentação técnica costuma
separar os dois em seções distintas, como se fossem preocupações
independentes.

Na prática, eles colidem o tempo todo, e é exatamente onde a
priorização importa. Um requisito funcional pode parecer resolvido de
forma simples, até esbarrar num requisito não funcional que ele
estava violando silenciosamente. Sem os dois catalogados lado a lado,
com peso relativo definido, essa colisão só aparece depois de
implementada, quando já custou tempo.

## Como isso fica registrado, na prática

O conjunto completo de requisitos funcionais ativos na versão atual do
blueprint, cada um com identificador e estado explícito:

| ID | Requisito | Status |
|----|-----------|--------|
| RF-TOOL-03 | Navegador de uso geral, isolado por perfil de trabalho | ✅ Implementado |
| RF-TOOL-03c | Ferramentas gráficas adicionais, instaladas sob demanda, isoladas por perfil | ✅ Implementado |
| RF-TOOL-05 | Gerenciamento consolidado de recursos, containers, imagens, variantes, com visão central e rótulo de segurança de remoção | ✅ Implementado |
| RF-TOOL-06 | Curadoria de toolset editável sem tocar em sintaxe de baixo nível | ✅ Implementado |
| RF-TOOL-07 | Validação preventiva de pacotes antes de qualquer build | ✅ Implementado |
| RF-UI-01 | Workspaces nomeados por perfil, organizados por fase de trabalho | 🔄 Parcial, um perfil completo e validado, dois replicando o mesmo padrão |
| RF-UI-04 | Painel de observabilidade do sistema em uso | ✅ Implementado, simplificado nesta versão |

E os não funcionais, na mesma disciplina:

| ID | Requisito | Status |
|----|-----------|--------|
| RNF-PERF-05 | Renderização gráfica estável sob virtualização aninhada | ✅ Implementado, validado extensivamente |
| RNF-PERF-06 | Memória suficiente sob carga de múltiplos containers simultâneos | ✅ Implementado, corrigido depois de um incidente real |
| RNF-SEC-09 | Isolamento de instalação de ferramenta por perfil, sem vazamento entre contextos | ✅ Implementado, revisado após violação identificada em produção |
| RNF-MAINT-05 | Runtime de container compatível com operações completas de sistema | ❓ Dívida técnica registrada, sem solução nativa disponível ainda |

Cada símbolo é uma categoria de prioridade, não decoração: implementado
e validado, parcialmente resolvido, revisado desde a versão anterior,
ou pendente de decisão explícita. Nenhum item circula sem estado. É
essa regra simples que transforma uma lista em instrumento de
trabalho, e é por isso que o conjunto inteiro importa, não só os itens
mais chamativos.

## Prioridade não é opinião, é registro

A parte que gestão de projeto tradicional faz bem e engenharia de
sistemas frequentemente pula: todo requisito tem uma prioridade
explícita, não implícita na cabeça de quem decidiu. O exemplo mais
claro está na última linha da segunda tabela. Um problema real, sem
solução nativa disponível, foi registrado como pendência formal em vez
de ser escondido ou adiado silenciosamente. Isso também é uma forma de
priorização, só que feita corretamente: expõe o que ainda não está
resolvido, em vez de fingir que está.

## O blueprint como instrumento, não como registro morto

A maior mudança de postura foi tratar o documento de requisitos como
algo que se atualiza junto com o sistema, não como um artefato que se
escreve uma vez no início e se abandona. Cada revisão significativa do
blueprint corresponde a uma mudança real de entendimento: uma decisão
que se mostrou errada sob teste, uma restrição que só apareceu depois
que o sistema rodou em condições reais, como aconteceu com o requisito
de isolamento por perfil, revisado depois de uma implementação
simples demais violar um princípio já registrado.

Isso só funciona com disciplina de gestão por trás. Sem ela, um
blueprint vira uma lista de desejos que ninguém revisita, e a
arquitetura acaba sendo decidida no improviso da implementação, não no
raciocínio que deveria guiá-la.

Essa é a base que sustenta tudo que venho publicando aqui. Antes de
qualquer decisão técnica interessante, existe uma decisão de
prioridade por trás, e é essa camada que os próximos textos desta
série vão continuar explorando.
