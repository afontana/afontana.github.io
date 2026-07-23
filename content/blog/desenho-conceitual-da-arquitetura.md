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

A resposta virou este desenho, organizado em camadas, com os perfis
de trabalho como o eixo central.

{% mermaid(invertible=true) %}
graph TD
    Manifesto["Manifesto vivo"] -.governa.-> Perfis
    Base["Base imutável"] --> Perfis

    subgraph Perfis["Perfis de trabalho"]
        P1["Invasão"]
        P2["Defesa"]
        P3["Perícia forense"]
        P4["Análise de malware"]
    end

    Perfis --> Fases["Fases de trabalho"]
    Fases --> Exec["Container, ou máquina virtual no perfil de malware"]
{% end %}

## As camadas, de baixo para cima

**Base imutável.** O sistema operacional em si não muda sem passar
por um processo explícito de transação, com snapshot e possibilidade
de reverter. É o ponto fixo de tudo que vem depois.

**Perfis de trabalho.** Sobre essa base, quatro perfis isolados:
invasão, defesa, perícia forense e análise de malware. Cada um vive
no seu próprio espaço, sem vazamento de ferramenta ou configuração
entre eles. Essa camada é o eixo do desenho inteiro, porque é aqui
que a ideia de isolamento vira decisão concreta, não princípio
abstrato.

**Fases dentro de cada perfil.** Cada perfil se divide em momentos de
trabalho específicos. Uma fase de reconhecimento não carrega o
ferramental de uma fase de exfiltração, mesmo estando no mesmo
perfil.

**Execução proporcional ao risco.** A maioria das fases roda em
container, leve e descartável. A análise de malware, que lida com o
material mais perigoso, roda numa máquina virtual completa, com
fronteira de kernel própria.

**Manifesto vivo, no topo.** Não é uma camada empilhada como as
outras. Governa o que entra em cada fase de cada perfil, atravessando
todo o resto em vez de ficar ao lado.

## Por que essas camadas juntas, e não menos

Cada camada existe porque, sozinha, alguma das outras deixaria uma
pergunta sem resposta.

Base imutável sem perfis isolados ainda seria um ambiente opaco: um
sistema estável, mas monolítico, onde tudo se mistura. Perfis
isolados sem uma base confiável não teriam onde se ancorar: o
isolamento não vale nada se o chão embaixo dele pode ser alterado sem
aviso. Fases sem execução proporcional ao risco tratariam um scan de
rede e uma análise de malware ativo com o mesmo nível de contenção, o
que é simplesmente errado.

E nenhuma dessas camadas, sozinha, resolve a pergunta de proveniência:
por que confiar no que está instalado em cada fase de cada perfil. É
para isso que existe o manifesto vivo.

## O que este desenho não mostra

Um diagrama de arquitetura sempre mente por omissão. Este aqui não
mostra os becos sem saída que precisaram ser percorridos até chegar
nesse formato, nem as vezes em que um perfil foi implementado de um
jeito, testado, e revertido porque violava um princípio dos outros
três. Essa parte, a mais honesta e a mais difícil de desenhar num
quadro, é o assunto dos próximos textos.
