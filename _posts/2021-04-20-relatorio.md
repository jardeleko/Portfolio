---
layout: post
title:  "Configurando broker Mosquitto (Escalabilidade)"
date:   2022-04-21 20:24:06 -0300
categories: jekyll update
---

Este Tutorial é indicado aos usuários de distros baseadas em `Debian`. Caso contrário, parte das configurações (talvez) não sejam validas, let's bora.

As distros baseadas em Debian possuem um limite de arquivos a serem abertos, definidos por padrão em no máximo 1024 arquivos simultâneos. Para que os testes em escala do `broker Mosquitto` sejam executáveis nestes sistemas, uma alteração no arquivo *system.conf* é fundamental.

A rota utilizada para encontrar este arquivo é */etc/systemd/system.conf*; 

Então, busque a váriavel DefaultLimitNOFILE que estará comentada, defina-a para o valor máximo de arquivos que você deseja abrir em simultâneo, neste exemplo 65535. 

{% highlight ruby %}
DefaultLimitNOFILE = 65535
{% endhighlight %}

Entretando, o `broker Mosquitto` possui um limite rígido de conexões, e em sua documentação, para ajustar este limite, deve-se abrir o arquivo [mosquitto.conf][mosquitto.conf], que se encontra em */etc/mosquitto/mosquitto.conf* e incluir a variável max_connections; 

{% highlight scheme %}
 max_connections -1
{% endhighlight %}

O valor -1 consiste em ilimitadas conexões.

No manual do [mosquitto.conf][mosquitto.conf] é aconselhado uma alteração no arquivo *limits.conf*, encontrado no diretório */etc/security/limits.conf*. Logo, devemos inserir duas linhas que reforçam os limites rígidos e também de software do sistema, ambos definidos em 65535.

{% highlight scheme %}
#<domain>      <type>  <item>         <value>
#
*               hard    nofile         65535
*               soft    nofile         65535
# End of file
{% endhighlight %}

Enfim, para que as atualizações façam efeito, torna-se necessário reinicializar o `daemon` do sistema e também o `serviço Mosquitto`.

<br>

[mosquitto.conf]: https://mosquitto.org/man/mosquitto-conf-5.html