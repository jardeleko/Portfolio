---
layout: post
title:  "Transactions com Node js e Postgresql"
date:   2022-10-27 01:14:06 -0300
categories: jekyll update
---

Simples população de um banco de dados `Postgres`, onde o cenário foi criado para inclusão da tabela Tuss em uma clínica médica, o desenvolvimento deu-se em `node js`, justamente por ter um escopo do projeto predefinido em BD2, disciplina qual cursei durante a graduação. Embora não seja algo absurdamente complexo em outras linguagens, em node, confesso ter certa simplicidade, inclusive, indica-se para um bom desempenho a adoção de linguaguens como `C` ou `Python`.  

O caso consistiu em diversas etapas divertidas, como por exemplo, a elaboração de um evento local antes do lançamento na `Cloud`, unicamente para garantir a integridade. Além da sanitização do lista que pode ser encontrada em [Tuss].

Enfim, o código também serve para demais consultas em serviços hospedados, em razão, decidi publicar no portfólio buscando reforçar minha animação com este caso específico.

Abaixo as poucas linhas de códigos que me satisfizeram como entusiasta de `SGBDs`, em principal `Postgresql`, adotado em grande parte do curso, tks `UFFS`:

~~~js
const dotenv = require('dotenv');
var data = require('./files.json');
dotenv.config();

var tmp; 
let conn;
let i = 0;

async function runTransaction() {
    connect();
    await createTable();
    await execute();
    await showRows();
}

//Connect to database with postgres user and pass
function connect() {
  today = new Date();
  const { Client } = require('pg');
  conn = new Client({
    host: process.env.HOST_HOMEUP,
    database: process.env.DB_HOMEUP,
    user: process.env.USER_HOMEUP,
    password: process.env.PASSWD_HOMEUP,
    // port: 5432,
  });
  conn.connect();
}

async function createTable() {
  await conn.query(`
      drop table if exists tuss_tables;
      create table if not exists tuss(
        code integer,
        proccess varchar,
        primary key (code)
      )
  `);
console.log('Table created in postgresql');
}

async function execute(){
  today = new Date();
  try {
    const values = data.map((s) => ({
      code: s.codigo,
      proccess: s.procedimento,
    }))

    await conn.query("BEGIN")
    while (i < values.length) {
      try {
      //Declare string for sql statement
        const sqlString = `INSERT INTO tuss_tables (code, proccess) VALUES ($1, $2);`;
          //Pass sql string to the query method
        let aux = [values[i].code, values[i].proccess]
        await conn.query(sqlString, aux, function(err, result) {
        console.log("client.query() SQL result:", result);
        if (err) {
          console.log("\nclient.query():", err);
          //Rollback before executing another transaction
          conn.query("ROLLBACK");
          console.log("Transaction ROLLBACK called");
        } else {
          conn.query("COMMIT");
          console.log("client.query() COMMIT row count:", result.rowCount);
        }
      });
      } catch (er) {
        //Rollback before executing another transaction
        conn.query("ROLLBACK");
        console.log("client.query():", er);
        console.log("Transaction ROLLBACK called");
      }
      i++
    }
  } finally {
    console.log("Tuss released");
  }
}

//Print all rows in console
async function showRows() {

  let { rows } = await conn.query(
    `SELECT * FROM tuss_tables;`
  );
  for (const row of rows) {
    console.log(row);
  }
  tmp = (new Date() - today);
  console.log("\nWas inserted " + i + " rows in " + tmp + " ms(t)");

  //Kill process in node, go back terminal
  process.exit();
}

runTransaction();

~~~

O fonte deste projeto pode estar sendo acessado neste [repositório][repositório], entretanto, o caso de uso busca análisar o desempenho do próprio Postgres em relação a inserção de 100k tuplas.

<br>

[repositório]: https://github.com/jardeleko/class-2020-extras/blob/main/BD2/TP_01/transaction_db.js

[Tuss]: https://github.com/charlesfgarcia/tabelas-ans
<!-- [Stripe]: https://stripe.com -->