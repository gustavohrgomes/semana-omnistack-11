# anotações aula 2 - semana omnistack

### Rota e Recurso

> caminhocompleto.com/user

o que vem depois da barra geralmente chamamos de recurso, rota seria o caminho completo que aplicação está fazendo.
recurso é aquilo que queremos buscar.

### Métodos HTTP

> GET: Buscar/listar uma info no back-end;
> POST: criar uma info no back-end;
> PUT: Alterar uma info no back-end;
> DELETE: Deletar uma info no back-end;

### Insomnia

### Tipos de parametros

Query params: Parâmetros nomeados enviados na rota após "?" (Filtro, paginação)

> http://localhost:3333/users?name=Cleiton


Route Params: Parâmetros utilizados para identificar recursos

````js
app.post('/users/:id', (request, response)
````

Request Body: Corpo da requisição, utilizado para criar ou alterar recursos

````json
{
	"name": "Diego Fernandes",
	"idade": 25
}
````

## Diferenças entre bancos de dados

SQL: MySql, sqlite, PostgreSQL, Oracle, SQL Server
NoSQL: MongoDB, CouchDB, etc.

### Configurando o banco de dados

Banco utilizado na aplicação será SQLite

Driver: SELECT * FROM users
Query Builder: table('users').select('*').where()

Query Builder utilizado na aplicação Knex.js

Instalando o Knex.js

>$ npm install knex --save
>$ npm install sqlite3

após instalado, iniciamos o knex.js

>$ npx knex init

### Desacoplando rotas do index.js

Para uma melhor organização das rotas, nós desacoplamos o módulo de rotas do express do arquivo _index.js_ para uma outra variável dentro de um arquivo chamado _routes.js_. Importamos o express com _require('express')_.

````js
const express = require('express');

const routes = express.Router();

routes.post('/users', (request, response) => {
    const body = request.body;

    console.log(body)

    return response.json({
        evento: "Semana Omnistack 11.0",
        aluno: "Cleiton Souza"
    });
});

module.exports = routes;
````

## Entidades e Funcionalidades da aplicação

#### Entidades
- ONG
- Casos (incident)

#### Funcionalidades
- Login da ONG
- Logout da ONG
- Cadastro de ONG
- Cadastrar Novos casos
- Deletar Casos
- Listar casos específicos de uma ONG
- listar todos os casos
- Entrar em contato com a ONG

#### Criando as tabelas

Antes de criar as tabelas, criaremos as migrations, que são como se fossem o controle de versão do nosso banco de dados.

Antes de criar as migrations, precisamos configurar o diretório delas no arquivo _knexfile.js_, adicionando a seguinte linha:

```js
    migrations: { 
      directory: './src/database/migrations'
    },
    useNullAsDefault: true,
```

Para criar as migrations utilizaremos os comandos

> $ npx knex migrate:make create_ongs
> $ npx knex migrate:make create_incidents

Após isso iremos escrever o seguinte código em cada migration para criar as tablelas: 

````js
exports.up = function(knex) {
    return knex.schema.createTable('ongs', function(table){
        table.string('id').primary();
        table.string('name').notNullable();
        table.string('email').notNullable();
        table.string('whatsapp').notNullable();
        table.string('city').notNullable();
        table.string('uf', 2).notNullable();
    })
};

exports.down = function(knex) {
    return knex.schema.dropTable('ongs');
};
````
> up: se tudo ocorrer bem
> down: caso algo dê errado ao executar a migration

_OBS: Cada tabela tem sua própria migration, portanto os campos devem ser adaptados conforme as necessidades das tabelas_

Após as migrations estarem escritas, rodamos o comando: 

> $ npx knex migrate:latest

Para criar nossa tabela.

### Criando as Controllers

- Criar uma pasta _Controllers_ na pasta _src_
- Dentro dessa pasta criar um arquivo chamado _OngController.js_
- E nesse arquivo, passar toda a lógica da rota Create dentro de um objeto module.exports

**OBS: Não esquecer de importar a conexão com o banco e o crypto para dentro da controller**

````js
const connection = require('../database/connection');
````
O mesmo é feito para a rota de listagem.

#### IncidentController 

Após a criação da _IncidentController_, nós fazemos o mesmo processo da OngController utilizando a desestruturação para fazer a requisição, porém agora nós temos que criar um caso para uma ong em específico, passando o ID dessa ong para a requisição. 

O ID da ONG representa qual ONG está autenticada no sistema, então não seria correto passar esse id através do corpo da requisição. Quando falamos de autenticação nós passamos o ID através do Cabeçalho da requisição.