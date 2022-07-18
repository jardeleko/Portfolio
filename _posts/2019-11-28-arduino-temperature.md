---
layout: post
title:  "Métricas do clima com Arduino UNO"
date:   2019-11-28 13:57:00 -0300
categories: jekyll update
--- 

Como esse portfólio foi criado prioritáriamente para lançar alguns projetos que eu gosto, não poderia deixar de selecionar um projetinho embarcado, envolvendo `Arduino Uno` e uma plaquinha `BME280`.

Mas antes, vou tentar explicar as razões de ter implementado esse serviço, vamos lá.

A ideia começou na disciplina de `Iniciação à Prática Científica` (IPC) e consistia em temporizar a irrigação de uma lavoura, inclusive, esse serviço já existe a algum tempo, mas fuçar nisso foi gratificante. Enfim, para que fosse possível, uma das etapas do projeto foi a coleta da temperatura/pressão/umidade, até porque, se houvesse umidade suficiente, não seria necessário o pivo, gotejamento ou qualquer outro método de irrigação ser ativado. Após essa coleta, um `protocolo` de minha preferência enviaria os dados coletados até um `servidor na nuvem`. 

No `Arduino`, os fios/junpers foram conectados na seguinte ordem:

| Arduino UNO | BME 280 |
|-------|-------|
| 5V    | VIN   |
| GND   | GND   |
| SCL   | SCL   |
| SDA   | SDA   |

Em seguida, bastava subir o código no UNO, ou assim dizendo, montar a sketch. 

Foi implementado um delay de 1000ms, ou seja, intevalos de  um em um segundo, até porque o dispositivo ia ficar operando direto, vide o Arduino consumir pouquíssima energia, o código abaixo apresenta as linhas que determinaram essas funções.

{% highlight c++ %}#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>

#define SEALEVELPRESSURE_HPA (1013.25)

Adafruit_BME280 bme;

void setup() {
	Serial.begin(9600);

	if (!bme.begin(0x76)) {
		Serial.println("Could not find a valid BME280 sensor, check wiring!");
		while (1);
	}
}

void loop() {
	Serial.print("Temperature = ");
	Serial.print(bme.readTemperature());
	Serial.println("*C");

	Serial.print("Pressure = ");
	Serial.print(bme.readPressure() / 100.0F);
	Serial.println("hPa");

	Serial.print("Approx. Altitude = ");
	Serial.print(bme.readAltitude(SEALEVELPRESSURE_HPA));
	Serial.println("m");

	Serial.print("Humidity = ");
	Serial.print(bme.readHumidity());
	Serial.println("%");

	Serial.println();
	delay(1000);
}
{% endhighlight %}

As imagens abaixa ilustram como ficou as plaquinas após a conexão dos fios.

<img src="https://raw.githubusercontent.com/jardeleko/Arduino/master/termostato/test/plac_.jpeg?token=GHSAT0AAAAAABT2BNMSRXYQIKWCB74SBYXIYTEWAWQ" width=350 height=300 style="float:left">

<img src="https://raw.githubusercontent.com/jardeleko/Arduino/master/termostato/test/protoboard.jpeg?token=GHSAT0AAAAAABT2BNMT7XK66PO2AMMKCQPOYTEWBAQ" width=400 style="float:right">

E os dados encontrados durante os testes foram estes:
<center>
<img src="https://raw.githubusercontent.com/jardeleko/Arduino/master/termostato/test/1.png?token=GHSAT0AAAAAABT2BNMTLXNDTAUW6OQV5QCAYTEWAEQ">
</center>
Enfim, também foi utilizado o Google Cloud Platform para testar os envios e entregas dos dados, porém a pesquisa não foi finalizada, isso pela falta do próprio acesso a sistemas de irrigação complexos.