---
layout: post
title:  "Pesquisa acadêmica do protocolo MQTT"
date:   2022-04-20 20:24:06 -0300
categories: jekyll update
---

<p>A Internet of Things (IoT) surgiu em 1999 através do Massachusetts Institute of Techno-
logy (MIT) e foi conceituado para comunicação Machine to Machine (M2M), ou seja, máquinas
se comunicando entre si (sensores, conexão de eletrônicos, controladores, etc) por meio de
Bluetooth, Radio-Frequency Identification (RFID), Wireless Fidelity (WIFI) e/ou rede cabeada.
Estes dispositivos se comunicam em cima de uma camada de rede, utilizando diversos protoco-
los e servidores na nuvem que hospedam e disseminam as informações publicadas. Somando
a quantidade de conexões suportadas e o baixo consumo de energia dos sensores, a rede IoT
cresceu de uma maneira exponencial nos desenvolvimentos de projetos de mobilidade urbana,
automação residencial e industrial.</p>
<p>Segundo a Telecommunication Union (ITU) (12), a internet das coisas é “Uma infra-
estrutura global para a sociedade da informação, permitindo serviços avançados através da
interconexão (física e virtual) de coisas baseadas em tecnologias interoperáveis de informação
e comunicação, existentes e em evolução ". Em uma análise estimativa, a IoT tem probabilidade
de crescer com ganhos de 25 a 50 bilhões de dispositivos conectados até 2025. De acordo com
Manyika (15), as principais áreas de potencial investimento são a saúde, agricultura e fabricantes
que utilizam sensores para otimizar a manutenção de equipamentos e proteger a segurança dos
trabalhadores. Em sua análise ascendente, é dimensionado um impacto econômico com poten-
cial total de U$3,9 trilhões a U$11,1 trilhões por ano até 2025, o que seria aproximadamente
11% da economia mundial.</p>
<p>Para que haja a possibilidade de uma comunicação neste modelo, é necessário que exista
uma arquitetura de rede que permita o envio e o recebimento de todas essas informações.
Os pacotes de dados necessitam de identificação para serem entregues em seus destinos, essa
comunicação é feita através de protocolos, como Transmission Control Protocol (TCP), User
Datagram Protocol (UDP), entre outros. Silva (23) documenta, que tanto o TCP quanto o
UDP funcionam em cima do Internet Protocol (IP), o IP é responsável pelo endereçamento das
máquinas, garantindo que estas estejam identificadas na rede. Outros protocolos comumente
visto em rede IoT, são: protocolo Message Queue Telemetry Transport (MQTT), aplicado em
cima do TCP/IP, que utiliza de agentes mensageiros nomeados como publisher e subscriber,
estes se comunicam mediante um broker atuante como um servidor; Constrained Application
Protocol (CoAP), que é um protocolo de padrão Request For Comments (RFC), normalmente
utilizado em aplicação de sensores vide sua simplicidade; Extensible Messaging and Presence
Protocol (XMPP), um protocolo aberto baseado em Extensible Markup Language (XML), com
padrão Internet Engineering Task Force (IETF), projetado para uso de aplicações de mensagens
instantâneas em tempo real.</p>
<p>Completando o cenário da comunicação M2M, na maioria dos casos é fundamental
a existência de um gateway, onde atua como responsável pela distribuição dos pacotes de
mensagens lançados por cada sensor, podendo também ser nomeado como um tradutor de
protocolos nesta mediação. O gateway no IoT está agregado ao conceito de Fog Computing
(Computação de borda ou computação em névoa), pois se encontra exatamente entre os sensores
e a Cloud (nuvem) e permitem que mais coisas sejam introduzidas em operações de conexão
em escala industrial, isto por suportarem diversos protocolos, ou serem implementados como
multiprotocolos devido a aplicação em cenários difíceis da indústria 4.0 e cidades inteligentes.
Entretanto, Silva (23) comenta em sua monografia, que existem desafios para o futuro
da IoT nos próximos anos, como a regulamentação, padronização e segurança, isto por motivo
de ter muitos protocolos implementados para rede IoT, onde cada protocolo tende a resolver
somente um problema, se mantendo restrito quanto à utilização geral. Ele também fomenta,
que a estimativa da inclusão do IoT em quase todos os dispositivos móveis ocorra em um
curto espaço de tempo, sendo os dispositivos: medidores de estacionamento, pneus, estradas,
monitores cardiovasculares, termostatos, sensores de presença, prateleiras de supermercados,
assim dizendo, uma imensa quantia de dispositivos conectados com a internet das coisas, gerando
uma propensa exploração das vulnerabilidade, problemas de privacidade com a disseminação
dos dados sensíveis e segurança com possíveis cenários de ataques. Por estas circunstâncias
são necessários regulamentos adequados, que evitem arriscar a segurança dos dispositivos e a
validade da IoT do futuro.</p>
<p>Enfim, a partir da análise do crescimento da internet das coisas, surge a proposta visando
explorar alguns destes exemplos citados, em principal o protocolo MQTT. O cenário selecionado
é definido por um dispositivo Raspberry Pi 4 como servidor/broker e um computador fazendo
disparos de publicações e/ou assinaturas, para então explorar o gargalo do dispositivo, com
intuito de analisar custos de processamento, o índice de utilização de memória, tráfego da rede
e as taxas de envio/entrega das mensagens.</p>


Para ler os resultados desta pesquisa, acesse [TCCII][TCCII] .

<br>

[TCCII]: https://github.com/jardeleko/2022/tree/main/TCCII
