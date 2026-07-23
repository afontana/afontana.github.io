+++
title = "Um dia real dentro do laboratório"
date = 2026-07-22
description = "Não é sobre a ferramenta. É sobre o que dá pra aprender olhando como ela se comporta em uso real."

[taxonomies]
tags = ["arquitetura", "segurança", "sistemas imutáveis"]
+++

O desenho conceitual da arquitetura mostra as camadas. Estas três
telas mostram o que acontece quando alguém realmente está usando o
sistema, e por que cada decisão de design descrita no texto anterior
aparece de forma concreta aqui.

![Visão geral de um workspace de fase, com barra de status mostrando perfil, fase ativa e estado do sistema](/img/print-geral.png)

Este é um workspace dedicado a uma fase específica de trabalho. A
barra de status no topo mostra o perfil ativo, o estado de segurança
do sistema, uso de recursos, e a fase atual com indicação de se o
ambiente já está construído ou precisa ser criado. Nada disso é
decorativo: cada campo responde a uma pergunta real que alguém
trocando de contexto de trabalho precisa responder em menos de um
segundo, sem digitar nenhum comando.

![Painel de administração mostrando containers, imagens, fases e variantes com rótulos de segurança de remoção](/img/lab-admin-detalhe.png)

Este painel existe porque, cedo no desenvolvimento, um incidente real
de esgotamento de memória revelou que acúmulo silencioso de
containers e imagens era um risco não visível até já ter causado
problema. A resposta não foi só corrigir o sintoma. Foi construir
visibilidade: cada item aqui carrega um rótulo de segurança indicando
se é seguro remover, recente demais para mexer, ou ainda em uso.
Governança de recurso, não só desempenho.

![Tela de login mostrando os perfis de trabalho disponíveis para seleção](/img/tela-login.png)

E aqui está a decisão que atravessa tudo o resto: o isolamento entre
perfis de trabalho não é um conceito abstrato de arquitetura. É a
primeira escolha que qualquer pessoa faz ao ligar a máquina, antes de
qualquer comando, antes de qualquer ferramenta.

Nenhuma dessas telas prova que o sistema funciona bem. Prova que as
decisões descritas no texto anterior não ficaram só no papel. É essa
distância entre desenhar e ver funcionando que geralmente é onde um
projeto revela suas próprias falhas de raciocínio, e é sobre isso que
os próximos textos desta série vão tratar.
