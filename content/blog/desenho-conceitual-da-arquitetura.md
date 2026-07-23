+++
title = "O desenho conceitual da arquitetura"
date = 2026-07-22
description = "As peças que sustentam o laboratório que venho construindo, e por que se encaixam desse jeito."

[taxonomies]
tags = ["arquitetura", "segurança", "sistemas imutáveis"]

[extra]
mermaid = true
+++

Antes de qualquer decisão de implementação, teve uma pergunta simples
que guiou tudo: que peças esse sistema precisa ter para que eu confie
nele, mesmo sozinho, sem ninguém verificando meu trabalho por mim?

A resposta virou este desenho.

{% mermaid(invertible=true) %}
graph TD
    Base["Base imutável"] --> Perfis["Perfis de trabalho isolados"]
    Perfis --> Fases["Fases dentro de cada perfil"]
    Fases --> Exec["Container ou máquina virtual, conforme o risco"]
    Manifesto["Manifesto vivo"] -.governa.-> Fases
    Manifesto -.governa.-> Exec
{% end %}

## As quatro camadas

**Base imutável.** O sistema operacional em si não muda sem passar
por um processo explícito de transação, com snapshot e possibilidade
de reverter. É o ponto fixo de tudo que vem depois. Se essa camada
puder ser alterada silenciosamente, nenhuma garantia acima dela
significa nada.

**Perfis de trabalho isolados.** O laboratório não é um ambiente
único e genérico. É dividido em contextos de uso distintos, cada um
com seu próprio espaço, sem vazamento de ferramenta ou configuração
entre eles.

**Fases dentro de cada perfil.** Cada perfil, por sua vez, se divide
em momentos de trabalho específicos. Cada fase carrega só o
ferramental daquele momento, isolado das demais fases do mesmo
perfil.

**Execução proporcional ao risco.** Nem toda fase merece o mesmo tipo
de isolamento. A maioria roda em container, leve e descartável. A que
lida com o material mais perigoso roda numa máquina virtual completa,
com fronteira de kernel própria.

## Por que essas quatro juntas, e não menos

Cada camada existe porque, sozinha, alguma das outras deixaria uma
pergunta sem resposta.

Base imutável sem perfis isolados ainda seria um ambiente opaco: um
sistema estável, mas monolítico, onde tudo se mistura. Perfis
isolados sem uma base confiável não teriam onde se ancorar: o
isolamento não vale nada se o chão embaixo dele pode ser alterado sem
aviso. Fases sem execução proporcional ao risco tratariam um scan de
rede e uma análise de malware ativo com o mesmo nível de contenção, o
que é simplesmente errado.

E nenhuma dessas três camadas, sozinha, resolve a pergunta de
proveniência: por que confiar no que está instalado em cada fase. É
para isso que existe a quarta peça, o manifesto vivo, que atravessa
todas as outras em vez de ficar ao lado delas. Ele não é uma camada a
mais na pilha. É a governança que decide o que entra em cada fase, e
por quê.

## O que este desenho não mostra

Um diagrama de arquitetura sempre mente por omissão. Este aqui não
mostra os becos sem saída que precisaram ser percorridos até chegar
nesse formato, nem as vezes em que uma camada foi implementada de um
jeito, testada, e revertida porque violava um princípio das outras
três. Essa parte, a mais honesta e a mais difícil de desenhar num
quadro, é o assunto dos próximos textos.
