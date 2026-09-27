# 📝 To-Do List API

> API RESTful robusta e segura construída com **Java** e **Spring Boot** para gerenciamento de tarefas e contas de usuários, contando com autenticação customizada e criptografia de senhas.

---

## 🎓 Origem do Projeto

Este projeto foi desenvolvido durante o minicurso prático **Java com Spring Boot** promovido pela **Rocketseat**. O objetivo do treinamento foi aplicar conceitos avançados de arquitetura de software no ecossistema Java, segurança de APIs e persistência de dados.

---

## 📌 Funcionalidades

* **Gerenciamento de Usuários**: Cadastro de usuários com validação de unicidade de *username* e criptografia de senhas.
* **Gerenciamento de Tarefas**: Criação e acompanhamento de tarefas vinculadas aos usuários, contendo títulos, descrições, nível de prioridade e prazos definidos.
* **Autenticação Customizada**: Middleware de verificação HTTP (`OncePerRequestFilter`) que valida credenciais codificadas em Base64 via `Basic Auth`.
* **Segurança e Proteção de Dados**: Aplicação de hash de senhas utilizando o algoritmo **BCrypt** com alto nível de complexidade de salt.
* **Persistência de Dados**: Mapeamento objeto-relacional (ORM) com JPA/Hibernate, geração automática de UUIDs e timestamps de criação.

---

## 🛠️ Tecnologias e Dependências

* **Linguagem**: Java 17+
* **Framework**: Spring Boot (Spring Web, Spring Data JPA)
* **Banco de Dados**: H2 Database (Banco em memória para desenvolvimento) / PostgreSQL
* **Segurança**: BCrypt (`at.favre.lib:bcrypt`)
* **Utilitários**: Lombok, Anotações do Hibernate
* **Gerenciador de Dependências**: Maven / Gradle

---

## 📐 Arquitetura e Detalhes de Segurança

```
   Requisição HTTP
        │
        ▼
┌─────────────────────────┐
│   FilterTaskAuth        │ ◄── Decodifica Basic Auth e Valida
└──────────┬──────────────┘     o Hash da Senha via BCrypt
           │
           ├──► [401 Não Autorizado]
           ▼
┌─────────────────────────┐
│   TaskController        │ ◄── Processa os Endpoints de Tarefas
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│   TaskRepository (JPA)  │ ◄── Persiste no Banco de Dados (tb_tasks)
└─────────────────────────┘
```

### Fluxo de Autenticação

1. O cabeçalho da requisição deve conter: `Authorization: Basic <credenciais_em_base64>`.
2. O filtro `FilterTaskAuth` intercepta requisições enviadas para as rotas protegidas (ex: `/tasks/`).
3. As credenciais são decodificadas (`username:senha`) e buscadas no banco através do repositório `IUserRepository`.
4. O `BCrypt.verifyer()` valida se a senha em texto puro coincide com o hash armazenado.
5. Se for válida, a requisição prossegue na cadeia de filtros (`FilterChain`); caso contrário, um erro `401 Unauthorized` é retornado.

---

## 🚀 Como Executar o Projeto

### Pré-requisitos

* **JDK 17** ou superior instalado.
* **Maven** instalado (ou utilizar o wrapper Maven `./mvnw` incluso no projeto).

### Passo a Passo

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/victormuniz-dev/todolist.git
   cd todolist
   ```

2. **Execute a aplicação via Maven:**
   ```bash
   ./mvnw spring-boot:run
   ```

3. A aplicação estará disponível em `http://localhost:8080`.

---

## 📡 Endpoints da API

### 👤 Usuários (`/users`)

| Método | Endpoint | Descrição | Requer Autenticação |
| :--- | :--- | :--- | :---: |
| `POST` | `/users/` | Cadastra um novo usuário | ❌ Não |

**Exemplo de Corpo da Requisição (`POST /users/`):**
```json
{
  "username": "victormuniz",
  "name": "João Victor",
  "password": "suasenhasegura"
}
```

---

### 📋 Tarefas (`/task`)

| Método | Endpoint | Descrição | Requer Autenticação |
| :--- | :--- | :--- | :---: |
| `POST` | `/task/` | Cadastra uma nova tarefa | 🔒 Sim (Basic Auth) |

**Exemplo de Corpo da Requisição (`POST /task/`):**
```json
{
  "description": "Implementar integração do LangChain",
  "title": "Estudar Sistemas Agênticos",
  "startAt": "2026-10-01T08:00:00",
  "endAt": "2026-10-01T18:00:00",
  "priority": "ALTA",
  "idUser": "b3e0c4a1-8d2e-4b1a-9f5e-1a2b3c4d5e6f"
}
```

---

## 🗄️ Esquema do Banco de Dados

### Tabela `tb_users`
* `id` (UUID, Chave Primária)
* `username` (String, Único)
* `name` (String)
* `password` (String - Hash BCrypt)
* `createdAt` (Timestamp)

### Tabela `tb_tasks`
* `id` (UUID, Chave Primária)
* `idUser` (UUID, Chave Estrangeira)
* `title` (String, limite de 50 caracteres)
* `description` (String)
* `startAt` (LocalDateTime)
* `endAt` (LocalDateTime)
* `priority` (String)
* `createAt` (Timestamp)

---

## ✒️ Autor

Desenvolvido por **João Victor Muniz Cabral**
* **LinkedIn**: [Victor Muniz](https://www.linkedin.com/in/victor-muniz-9a0aa8345)
* **GitHub**: [@victormuniz-dev](https://github.com/victormuniz-dev)
