# Resumo Simples: API com Login e Posts

Este projeto é uma **API básica** com autenticação. Ele permite que usuários façam login e que, com um token JWT, possam acessar e manipular publicações (posts).

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

## Conclusão

Este projeto mostra de forma prática como proteger rotas com token, organizar pastas e lidar com requisições básicas (CRUD). Diferente de um sistema completo ele **não usa banco de dados real** e **tem menos entidades e funcionalidades**. Por isso, é mais indicado para fins de estudo, testes e protótipos simples.

---

