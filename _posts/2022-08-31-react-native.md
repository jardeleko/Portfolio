---
layout: post
title:  "Projeto Mobile Facilita Imóveis"
date:   2022-08-31 20:24:06 -0300
categories: jekyll update
---

A algum tempo estava evitando os projetos mobiles, razões: ferramentas não híbridas ou complexas e pesadas (embora já ter desenvolvido antes utilizando `Ionic` e `Java // Kotlin`), entretanto, o `React Native` junto com o `EXPO` ampliaram meu interesse em estar desenvolvendo pequenos projetos como este. Aliás, entre as app views testadas, o native foi um dos melhores em razão do bundler (considerando análise sintática). Enfim, para testar melhor a ferramenta, busquei um problema existente baseando-se no aplicativo Airbnb e utilizando templates encontrados em repositórios do Github. Sendo assim, o projeto consistiu em uma aplicação de aluguéis de casas (ou cômodos), garagens e salas comerciais; onde a principal característica pode ser definida pela locação sem intermediações (imobiliárias ou corretores), ou seja, os cliente ofertando seus imóveis e se comunicando entre si. 

Buscando não publicar uma documentação extensa, separei três etapas que deram identidade ao projeto, sendo estas: Chat implementado com a lib `Gifted Chat`, Autenticação via email (em razão de ter que desenvolver um app em cloud na `GCP`) e por fim, a redefinição de senha utilizando um servidor `SMTP`, para isto utilizei a `Sendinblue`. 

Curiosamente, passei uma certa dificuldade ao implementar o chat em razão da minha insistência em utilizar `websockets` através do framework `socket.io`, o que posteriormente vi que era impossível na versão atual do Gifted, ainda não sei porque, mas essas informações sempre estão em um limbo da internet e acabam roubando muito tempo de quem as necessita. Mas o resultado foi interessante, abaixo alguns trechos que exemplificam o uso desta lib.

~~~js
import { GiftedChat } from 'react-native-gifted-chat'
	const [messages, setMessages] = useState([])    //variáveis para lançar novas mensagens
    //normalmente essas variaveis são iniciadas antes, buscando dados historicos da conversação dos usuarios relacionados, publicador e current 
	const onSend = useCallback((messages = []) => {
		setMessages(previousMessages => GiftedChat.append(previousMessages, messages)) //empilha a nova mensagem
		asyncSubmitMessage(messages[0]) //função que envia a mensagem ao banco de dados
	}, [control])

    return (
        <GiftedChat //utilizando a ferramenta 
            messages={messages}
            onSend={text => onSend(text)}
            {% raw %}
            user={{
                _id: currentUser._id,
                idRec: idReceiver !== currentUser._id ? idReceiver : "errorId",
                name: currentUser.name,
                idHouse: idHouse
            }}
        {% endraw %}
        />
    )
~~~

O Google Cloud Platform é um servidor muito amigável, com ele, foi simples implementar o serviço externo (app ou página para autenticação). E assim que gerada as credenciais, a implementação deu-se da seguinte forma:

~~~js
import * as WebBrowser from 'expo-web-browser'
import * as Google from 'expo-auth-session/providers/google' //lib indicada pela doc do expo

WebBrowser.maybeCompleteAuthSession() //incia o webbrowser para autenticações
    const [accessTk, setAccessTk] = useState(null)
    const [userInfo, setUserInfo] = useState(null)
    const [request, response, promptAsync] = Google.useIdTokenAuthRequest({ //utilizando as credenciais criadas na nuvem
        expoClientId: '347880960520-sje8ods0rcbl5v6lpadu6pmsjo1p********.apps.googleusercontent.com',
        androidClientId: '347880960520-of68n10pgllkm6nn61ppnb5v351********.apps.googleusercontent.com',
        iosClientId: '347880960520-******************.apps.googleusercontent.com'
    })

    useEffect(() => { //use effect para verificar se a autenticação gmail foi iniciada, se sim, verifica o exito 
        if(response?.type ==='success'){ //se exito
            setAccessTk(response.params.id_token) //pega o token do usuario
            accessTk && fetchUserInfo() //se tem token chama a função para buscar dados do usuario
        }
    },[response, accessTk])

    async function fetchUserInfo(){ //aqui esta o pulo do gato, demorei um pouco p encontrar esta query
    //como tudo na vida tem um preço, este deve ter me custado umas 2 ou 3 horas hahaha 
        let response = await fetch(`https://oauth2.googleapis.com/tokeninfo?id_token=${accessTk}`) //dita query sobre quem eu falo
        const userInfo = await response.json() //se temos resposta temos json format
        setUserInfo(userInfo) //atribuindo os dados a minha variavel userInfo
    }
~~~

Na página da sendinblue, o serviço é praticamente resgatar as variáveis (caso escolha utilizar via smtp): host, port, cliente, no meu projeto, escolhi integrar desta forma, entretanto, há também uma opção de integração via api. No backend, a ferramenta necessária foi a lib `Nodemailer`, que garantiu a conexão com o serviço da sendin, permitindo o disparo de emails a todos os clientes que solicitarem a redefinição de senha, o fonte após a implementação:

~~~js
const nodemailer = require('nodemailer') //importa o nodemailer
const router = require('express').Router()

const transport = nodemailer.createTransport({  //n-mailer criando a variavel de conexão e transporte
  host: "smtp-relay.sendinblue.com",
  port: 587,
  auth: {
    user: "jardelduarte***@gmail.com",
    pass: "HIa4J1T0vsynw****"
  }
  router.post('/forgot', async (req, res) => { // iniciando a rota
  User.findOne({email:req.body.email}).then((user) => {
    if(!user){
      res.status(422).json('user not exists!')
    }
    const expired = Date.now() + (3600000/2) //variavel que gera um timeout de 30 minutos para expiração do token
    const aux = Math.floor(Math.random() * 65536) //o token de no minimo 4 digitos gerados ao aleatorio
    if(aux.length < 1234) aux = Math.floor(Math.random() * 65536)
    user.passwdToken = String(aux)
    user.expireToken = expired
    user.save().then((result) =>{
      console.log(result)
      transport.sendMail({ //apos verificar que o usuario existe no bd, a variavel global transport, envia o email com os dados do usuario e com o email padrão definido no html
        to:user.email,
        from: "no-replay@facilitaimoveis.com.br",
        subject: "Redifinição de Senha, Facilita Imóveis",
        html: "<p><h3>Olá, "+user.name+".<h3></p> <br><br> <p>Vimos que você solicitou a recuperação de senha.</p> <p>Para continuar, insira o código <strong>" + aux + "</strong>  na página de verificação iniciada no seu aplicativo..</p>",
      })
      res.status(200).json({token:aux, expired}) //se success, encaminha as variaveis para tratar no frontend
    }).then((err) =>{
      res.status(500).json(err)
    })
  }).catch((err) => {
    res.status(500).json(err)
  })
})
})

~~~

Enfim, gostei dos desafios durante esta construção, em principal pela facilidade de desenvolver mobile com a ferramenta EXPO, o código fonte pode ser acessado neste [repositório][repositório] e abaixo, algumas animações com o resultado final de duas etapas detalhadas nesta publicação.

![chat](https://firebasestorage.googleapis.com/v0/b/facilitastorage.appspot.com/o/chat.gif?alt=media&token=00c220ab-69a3-4ef4-8460-bc012e103245) | ![login](https://firebasestorage.googleapis.com/v0/b/facilitastorage.appspot.com/o/login.gif?alt=media&token=f77e33f7-ef3d-4d1b-851a-3327a22a4ef0) 

<br>

[repositório]: https://github.com/jardeleko/facilita
