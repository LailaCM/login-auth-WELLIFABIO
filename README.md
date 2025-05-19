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
const express = require('express');
const app = express();
require('dotenv').config();

const loginRoutes = require('./src/routes/login');
const postRoutes = require('./src/routes/posts');

app.use(express.json());
app.use('/login', loginRoutes);
app.use('/posts', postRoutes);

app.listen(3000, () => {
  console.log('Servidor rodando na porta 3000');
});
```

### O que faz:

- Inicia o servidor.
- Lê variáveis do arquivo `.env`.
- Define as rotas `/login` e `/posts`.
- Usa `express.json()` para aceitar dados JSON nas requisições.

---

## `src/middlewares/auth.js`

```js
const jwt = require('jsonwebtoken');

module.exports = (req, res, next) => {
  const token = req.header('Authorization');

  if (!token) return res.status(401).send('Acesso negado. Token não fornecido.');

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(400).send('Token inválido.');
  }
};
```

### O que faz:

- Verifica se há um token JWT no cabeçalho da requisição.
- Se o token for válido, libera o acesso à rota.
- Se não for, bloqueia o acesso e responde com erro.

---

## `src/controllers/login.js`

```js
const jwt = require('jsonwebtoken');

const login = (req, res) => {
  const { username, password } = req.body;

  if (username === 'admin' && password === '1234') {
    const token = jwt.sign({ username }, process.env.JWT_SECRET);
    res.json({ token });
  } else {
    res.status(401).send('Credenciais inválidas');
  }
};

module.exports = login;
```

### O que faz:

- Verifica se o usuário e senha são "admin" e "1234".
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
const posts = [
  { title: 'Primeiro post', content: 'Este é o conteúdo do primeiro post.' }
];

module.exports = posts;
```

### O que faz:

- Armazena os posts em um array (dados ficam em memória, não em banco de dados).

---

## `src/routes/login.js`

```js
const express = require('express');
const router = express.Router();
const login = require('../controllers/login');

router.post('/', login);

module.exports = router;
```

### O que faz:

- Define a rota `POST /login`.
- Chama o controller que gera o token se as credenciais forem corretas.

---

## `src/routes/posts.js`

```js
const express = require('express');
const router = express.Router();
const auth = require('../middlewares/auth');
const { getPosts, createPost, updatePost, deletePost } = require('../controllers/posts');

router.use(auth);

router.get('/', getPosts);
router.post('/', createPost);
router.put('/:id', updatePost);
router.delete('/:id', deletePost);

module.exports = router;
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
| Usuário fixo   | `admin` e senha `1234`               |
| Uso principal  | Estudo, protótipo, testes didáticos  |

# Conclusão

Este projeto mostra de forma prática como proteger rotas com token, organizar pastas e lidar com requisições básicas (CRUD). Diferente de um sistema completo ele **não usa banco de dados real** e **tem menos entidades e funcionalidades**. Por isso, é mais indicado para fins de estudo, testes e protótipos simples.

---

