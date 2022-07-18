---
layout: post
title:  "Sistemas Especialistas - IA"
date:   2021-12-08 13:57:00 -0300
categories: jekyll update
---

Projeto desenvolvido para o componente curricular de `Inteligência Artificial`, o intuito foi o desenvolvimento de um `sistema especialista` baseando-se nos modelos apresentados. Neste, fiz a escolha em elaborar um serviço no qual a máquina identifica o tipo de arte (visual e/ou digital) que o usuário tem mais interesse, foi implementado com a linguagem de programação Python. 

Durante a coleta dos dados, foi necessário construir um pequeno dataset identificando as relações hereditárias, listadas abaixo.

{% highlight ruby %} #se  -   então
colorido-surrealista 
colorido-renascentista
colorido-minimalista
colorido-artdigital
pb-renascentista
pb-minimalista
pb-artdigital
pixel-artdigital
abstrato-surrealista
fotofig-renascentista
geometrico-minimalista
{% endhighlight %}

Para construir a máquina foram criadas duas classes (`Pergunta e Identifica`) que posteriormente foram inclusas na função principal que executa o programa. 

A classe `Pergunta`, recolhe os valores (`S` ou `N`) que o usuário vai respondendo assim que o programa é executado, similar a um Quizz, onde cada pergunta possui uma resposta "correta" e que irá atribuir uma probabilidade em caso de acerto. 

{% highlight python %} from random import *
class Pergunta:
def __init__(self):
    self.level = [
    ['Você gosta de pinturas coloridas?','colorido'],
    ['Te agrada figuras abstratas, coisas distorcidas?','abstrato'],
    ['Você gosta de pinturas em preto e branco?','pb'],
    ['Para voce a arte pode ser vista digitalmente?','pixel'],
    ['Poucos detalhes, simbolos, textos e desenhos?','geometrico'],
    ['Pinturas de pessoas(rostos, lugares)?','fotofig'],		
    ]

def texto(self):
    string = self.level[0]
    del self.level[0]
    return string
{% endhighlight %}

A classe abaixo (`Identifica`) é responsavel por pegar a resposta do usuário e fazer a identificação a partir da lista dos possíveis cenários do nosso set hereditário. Buscando a probabilidade e removendo os casos que não existem relações com a resposta. 

{% highlight python %} class Identifica():
# metodo construtor
def __init__(self):
    self.resultado = ['surrealista', 'renascentista', 'minimalista', 'artdigital']
    self.pessoa = []
    self.db = []
# abre o arquivo db.txt em modo leitura e passa os dados para
# uma lista de listas de str
    arquivo = open('db.txt','r')
    for linha in arquivo:
        if linha[len(linha) - 1] == '\n':
            linha = linha.replace("\n", "")
            (self.db).append(linha.split('-'))
    arquivo.close()

# imprime a quantidade de possibilidades cadastradas
def tamanho(self):
    print(len(self.resultado))

# imprime a probabilidade do diagnótico
def probabilidade(self):
    return (int((1/int(len(self.resultado)))*100))

# verifica se diagnóstico pensado tem a caracteristica passada por parametro
def busca(self, familiar, caract):	
    for i in range(len(self.db)):
        if familiar == self.db[i][1] and self.db[i][0] == caract:
            return True
    return False				

# remove os diagnósticos que não possuem o atributo passado por parametro
def excluiquemnaoe(self, atributo):
    lista = []
    count = 0
    for i in range(len(self.resultado)):
        if not self.busca(self.resultado[i], atributo):
            lista.append(self.resultado[i])
            count = count + 1
    for i in range(count):
        self.resultado.remove(lista[i])
	
# remove os cenários que possuem o atributo passado por parametro
def excluiqueme(self, atributo):
    lista = []
    count = 0
    for i in range(len(self.resultado)):
        if self.busca(self.resultado[i], atributo):
            lista.append(self.resultado[i])
            count = count + 1
    for i in range(count):
        self.resultado.remove(lista[i])
    
def pergunta(self,pergunta, caract):
    resp = input(pergunta+': ')
    if resp == 's' or resp == 'S':
        self.excluiquemnaoe(caract)
    elif resp == 'n' or resp == 'N':
        self.excluiqueme(caract)

{% endhighlight %}

Enfim, a `main` executa o programa até que a probabilidade da característica seja 100%, enquanto não for, o programa continua perguntando ao usuário, no fim, retorna o tipo de arte que a pessoa tem propensão e mostra alguns pintores daquela característica, selecionados por mim. 

{% highlight python %} from classId import *
from classPerguntas import *

#Inferência
se = Identifica()
pergunta = Pergunta()


while se.probabilidade() != 100:
	string = pergunta.texto()
	se.pergunta(string[0], string[1])
	print('probabilidade é %d' %(se.probabilidade()))
	print(se.resultado)
	if se.probabilidade() == 100:
		print('Arte mais indicado para você é: ',se.resultado[0])
		if(se.resultado[0] == 'surrealista'):
			print("Pintores relacionados:\n Vicente Van Gogh \n Salvador Dalí \n Pablo Picasso \n")
		elif(se.resultado[0] == 'renascentista'):
			print("Pintores relacionados:\n Michelangelo \n Leonardo da Vinci \n Rafael \n")
		elif(se.resultado[0] == 'minimalista'):
			print("Pintores relacionados:\n Donald Judd \n Yayoi kusama \n Frank Stella \n")
		elif(se.resultado[0] == 'artdigital'):
			print("Pintores relacionados:\n Mike Winkelmann \n Banksy \n Giselle Beiguelman \n")
{% endhighlight %}

Link para acessar o conteúdo:[especialista-IA][especialista-IA].

<br>

[especialista-IA]: https://github.com/jardeleko/Python/tree/master/Machine%20Learning/Sistemas%20Especialistas