+++
title = "Três sintomas, uma causa: diagnosticando o ambiente gráfico sob virtualização aninhada"
date = 2026-07-23T21:00:00-03:00

[taxonomies]
tags = ["diário de bordo", "virtualização aninhada", "vmware", "wayland", "sway", "renderização gráfica", "gpu virtual", "kvm", "libvirt", "spice", "vnc", "arquitetura", "drivers"]
+++

**23 de julho de 2026, 21h**

Três problemas apareceram em momentos diferentes, cada um tratado como
incidente isolado quando surgiu: travamento severo de mouse em
qualquer aplicativo gráfico, componentes de interface ausentes para
hospedar máquinas virtuais adicionais dentro do próprio sistema, e
captura de mouse quebrada ao acessar essas VMs remotamente. Resolvi
os três pontualmente, cada um no momento em que doeu. Só ao parar pra
registrar o que tinha acontecido ficou claro que não eram três
problemas. Era um problema estrutural se manifestando três vezes: o
ambiente gráfico nunca tinha sido tratado como uma camada completa e
testada, só como uma sequência de remendos sob pressão.

## O sintoma mais grave, e a correção rasa que ele recebeu

O travamento de input veio do renderer padrão do compositor Wayland
tentando processar aceleração gráfica numa GPU virtual, sob
virtualização aninhada, de um jeito que simplesmente não se sustentava.
A correção que apliquei na hora funcionou: forçar renderização
inteiramente por software. Resolveu o sintoma. Mas tem um custo real
que eu não tinha medido, porque não parei pra medir: toda a composição
de tela agora depende de CPU, sem nenhuma aceleração de hardware.

Uma correção que funciona não é a mesma coisa que uma correção
entendida.

## A pergunta que eu não tinha feito

Por que distribuições consolidadas não sofrem esse mesmo travamento
rodando na mesma plataforma de virtualização? Investigando, a resposta
não é sorte nem configuração diferente. É que essas distros usam, por
padrão, uma pilha de driver dedicada e específica ao hardware virtual
daquela plataforma, mantida havia mais de uma década pelo próprio
fabricante em conjunto com a comunidade gráfica do Linux. Meu ambiente
estava usando o caminho genérico. Não tinha o driver certo instalado.

Isso muda a pergunta de "como eu corrijo o sintoma" para "por que o
driver certo não está aqui", que é uma pergunta bem mais incômoda de
fazer sobre o próprio trabalho.

## A parte que a pesquisa não deixa fingir que é simples

Mesmo com o driver correto, existe fartura de relato documentado, em
múltiplas plataformas de virtualização e projetos de hypervisor
diferentes, de degradação e instabilidade ao habilitar aceleração 3D
sob virtualização aninhada. Inclusive um caso reconhecido oficialmente
pelo próprio fabricante da plataforma que uso. A conclusão honesta não
é "vou trocar o driver e está resolvido". É que aceleração 3D sob VM
aninhada é estruturalmente frágil no ecossistema inteiro, não uma
falha exclusiva da minha configuração. A decisão entre renderização
acelerada e por software precisa vir de medição real neste ambiente
específico, não de suposição, e ainda não fiz essa medição.

## Uma decisão de arquitetura que decidi não tomar

Cheguei a considerar um script que detectasse o ambiente de
virtualização em tempo de execução e ajustasse driver e configuração
dinamicamente a cada inicialização. Descartei. Não porque fosse
impossível, mas porque adicionava complexidade e ponto de falha para
resolver um problema que já tem resposta conhecida no momento em que a
imagem do sistema é construída, não precisa ser descoberta a cada
boot. A plataforma-alvo é conhecida antes, não em tempo real.

A decisão foi seguir o padrão que distribuições maduras já usam:
variantes de instalação distintas por plataforma-alvo, cada uma já
vindo, de fábrica, com o que aquele ambiente específico precisa. Isso
tira a lógica condicional de dentro do sistema em execução e torna
cada variante testável isoladamente, sem depender de detecção
acertar.

## O que ainda está pela frente

O diagnóstico chegou a um roteiro, não a uma correção fechada:
confirmar se o driver dedicado está de fato presente e carregado;
testar o renderer acelerado com esse driver correto instalado, dessa
vez medindo estabilidade e desempenho de verdade em vez de assumir;
padronizar o protocolo de acesso gráfico às VMs hospedadas
internamente, trocando a configuração inicial por uma combinação que
evita estruturalmente o problema de captura de mouse já identificado;
e só then consolidar tudo isso como definição oficial de uma variante
de instalação, repetindo o mesmo processo de diagnóstico para cada
outra plataforma-alvo, sem assumir que a solução de uma se aplica às
demais.

Duas coisas que apareceram nesse caminho ficaram deliberadamente de
fora do escopo, registradas para depois: serviços básicos de sistema
ainda ausentes no ambiente gráfico, e uma ideia de identidade visual
por espaço de trabalho que se mostrou inviável na ferramenta atual sem
trocar de compositor. Nenhuma das duas é urgente. As duas estão
anotadas, não esquecidas.
