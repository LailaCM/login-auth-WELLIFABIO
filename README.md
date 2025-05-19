# Resumo

Este projeto é uma **API básica** com autenticação. Ele permite que usuários façam login e que, com um token JWT, possam acessar e manipular publicações 
(posts).

## Funcionalidades

- Login de usuários com geração de **token JWT**
- Middleware que protege as rotas usando o token
- Rotas para **criar, listar, atualizar e deletar posts**

## Tecnologias Usadas

- **Node.js** – Ambiente de execução JavaScript
- **Express.js** – Framework para criação de rotas e servidor
- **jsonwebtoken (JWT)** – Autenticação via token
- **dotenv** – Leitura de variáveis de ambiente

## Comparação com Projeto de Biblioteca

| Item                    | API Simples com Posts                         | Projeto de Biblioteca                             |
|-------------------------|----------------------------------------------|---------------------------------------------------|
| **Objetivo**            | Login e gerenciamento de posts               | Gerenciar alunos, livros e empréstimos           |
| **Banco de Dados**      | Não usa (dados armazenados em arquivo)       | Usa banco de dados real com Prisma ORM           |
| **Autenticação**        | Sim, com JWT                                 | Sim, mas pode ter outra abordagem                 |
| **Entidades**           | Usuários, Posts                              | Alunos, Livros, Empréstimos                      |
| **Complexidade**        | Baixa (projeto didático)                     | Alta ou moderada (projeto completo)              |
| **Documentação Visual** | Não possui                                   | Possui prints, DER e testes no Insomnia          |
| **Organização**         | Estrutura simples                            | Estrutura modular, organizada profissionalmente  |

## Explicando
---

## `server.js`

```js
require("dotenv").config();
const express = require('express');
const app = express();
const port = 3000;

const loginRoutes = require('./src/routes/login');
const postsRoutes = require('./src/routes/posts');

app.use(express.json());

app.use(loginRoutes);
app.use(postsRoutes);

app.listen(port, () => {
    console.log('listening on ' + port);
})
```

### O que faz:

- Inicia o servidor.
- Lê variáveis do arquivo `.env`.
- Define as rotas `/login` e `/posts`.
- Usa `express.json()` para aceitar dados JSON nas requisições.

---

## `src/middlewares/auth.js`

```js
const jsonwebtoken = require("jsonwebtoken");

const validate = (req, res, next) => {
    const token  = req.headers.authorization?.split(" ")[1];

    if(!token) res.status(401).send({message : "Access Denied. No token provided."}).end();
    
    try {
        const payload = jsonwebtoken.verify(token, process.env.SECRET_JWT);

        req.headers['user'] = payload;

        next();
    }catch(err) {
        res.status(500).send(err).end();
    }
}

module.exports = validate;
```

### O que faz:

- Verifica se há um token JWT no cabeçalho da requisição.
- Se o token for válido, libera o acesso à rota.
- Se não for, bloqueia o acesso e responde com erro.

---

## `src/controllers/login.js`

```js
const jsonwebtoken = require("jsonwebtoken");
const crypto = require('node:crypto');

const Login = (req, res) => {
    const { user, psw } = req.body;
    
    try {
        const correctPassword = ((user === "usuario@gmail.com") && (psw === "a1b2@b3c4"));

        if(!correctPassword) res.status(401).send({message:'E-mail or Password incorrect !'});

        const token = jsonwebtoken.sign(
            {
                id: crypto.randomUUID(),
                name: "Fulano da Silva",
                avatar: "https://cdn-icons-png.flaticon.com/128/1326/1326377.png"
            },
            process.env.SECRET_JWT,
            { expiresIn: "2min" }
        );

        res.status(200).json({ token : token }).end();
    }catch(err) {
        res.status(500).send(err).end();
    }
    
    res.status(200).end();
};

module.exports = {
    Login
}
```

### O que faz:

- Verifica se o e-mail e a senha são `"usuario@gmail.com"` e `"a1b2@b3c4"`.
- Se sim, gera um token JWT com base no nome de usuário.
- Envia o token para o cliente usar nas próximas requisições.

---

## `src/controllers/posts.js`

```js
let posts = require('../data/posts');

const getPosts = (req, res) => res.json(posts);

const createPost = (req, res) => {
  const post = req.body;
  posts.push(post);
  res.status(201).json(post);
};

const updatePost = (req, res) => {
  const id = parseInt(req.params.id);
  posts[id] = req.body;
  res.json(posts[id]);
};

const deletePost = (req, res) => {
  const id = parseInt(req.params.id);
  posts.splice(id, 1);
  res.status(204).send();
};

module.exports = { getPosts, createPost, updatePost, deletePost };
```

### O que faz:

- Manipula as ações dos posts:
  - `GET`: retorna todos os posts.
  - `POST`: adiciona um novo post.
  - `PUT`: atualiza um post pelo índice (id).
  - `DELETE`: remove um post pelo índice.

---

## `src/data/posts.js`

```js
const dataPosts = require("../data/posts");

const posts = (req, res) => {
    res.status(200).send(dataPosts).end();
}

module.exports = {
    posts
}
```

### O que faz:

- Armazena os posts em um array (dados ficam em memória, não em banco de dados).

---

## `src/routes/login.js`

```js
const loginController = require('../controllers/login');

const express = require('express');

const loginRoutes = express.Router();

loginRoutes.post('/login', loginController.Login);

module.exports = loginRoutes;
```

### O que faz:

- Define a rota `POST /login`.
- Chama o controller que gera o token se as credenciais forem corretas.

---

## `src/routes/posts.js`

```js
const postsController = require("../controllers/posts");
const validate = require("../middlewares/auth");

const express = require('express');

const postsRoutes = express.Router();

postsRoutes.get('/posts', validate, postsController.posts);

module.exports = postsRoutes;
```

### O que faz:

- Todas as rotas de post passam primeiro pelo middleware `auth`.
- Só permite acesso a quem estiver autenticado.
- Permite ver, criar, editar e apagar posts.

---

## Resumo 

| Item              | Descrição                            |
| ----------------- | ------------------------------------ |
| Autenticação   | JWT via rota `/login`                |
| Armazenamento | Em memória (sem banco de dados)      |
| Rotas          | `/login`, `/posts`                   |
| Segurança      | Middleware protege as rotas de posts |
| Usuário fixo       | `usuario@gmail.com` e senha `a1b2@b3c4`         |
| Uso principal  | Estudo, protótipo, testes didáticos  |

# Conclusão

Este projeto mostra de forma prática como proteger rotas com token, organizar pastas e lidar com requisições básicas (CRUD). Diferente de um sistema completo ele **não usa banco de dados real** e **tem menos entidades e funcionalidades**. Por isso, é mais indicado para fins de estudo, testes e protótipos simples.

---

