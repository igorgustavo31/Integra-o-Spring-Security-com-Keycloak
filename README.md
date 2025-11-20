# 🔐 Spring Boot + JWT + Keycloak Demo

Este projeto é um exemplo prático de integração entre **Spring Boot (3+)**, **JWT (JSON Web Token)** e **Keycloak (21+)** para autenticação e autorização de APIs REST.  
O objetivo é demonstrar como proteger endpoints com diferentes níveis de acesso (público, autenticado e administrador) utilizando **OAuth2 Resource Server** e **Keycloak** como Authorization Server.

Esse projeto foi desenvolvido como atividade da disciplina **Arquitetura de Aplicacoes Web** ministrada pelo professor Leonardo Vieira.

---

## 🚀 Tecnologias Utilizadas

- **Java 17+**
- **Spring Boot 3+**
- **Spring Security**
- **Spring OAuth2 Resource Server**
- **Keycloak 21+**
- **Maven** (ou Gradle)
- **Postman / curl** (para testes)

---

## ⚙️ Configuração do Keycloak

### 1. Instalar e Iniciar o Keycloak
Baixe e execute o Keycloak localmente.  

Exemplo (no diretório descompactado do Keycloak):

```bash
bin/kc.sh start-dev
````

Por padrão, o Keycloak ficará disponível em:

```
http://localhost:8080
```

### 2. Criar um Realm

Acesse o console de administração: [http://localhost:8080](http://localhost:8080)

Faça login com o usuário administrador criado na instalação

Crie um novo Realm chamado: `demo`

### 3. Criar um Client

Dentro do Realm `demo`, vá até **Clients → Create**

* Nome do Client: `spring-api`
* Access Type: `confidential`
* Standard Flow Enabled: `true`
* Direct Access Grants Enabled: `true`
* Valid Redirect URIs: `http://localhost:8080/*`

Salve e gere o Client Secret.

### 4. Criar Roles

Vá em **Realm Roles → Add Role**:

* `user`
* `admin`

### 5. Criar Usuários

Vá em **Users → Add User**:

* Usuário: `user_demo` → Role: `user`
* Usuário: `admin_demo` → Role: `admin`

### 6. Obter o Issuer URI

Após salvar tudo, copie o valor do Issuer URI do Realm:

```
http://localhost:8080/realms/demo
```

**Este valor será usado no `application.yml`.**

---

## 🧩 Estrutura do Projeto Spring Boot

```
src/
 └── main/
     ├── java/
     │   └── com/igorgustavo/demo/
     │       ├── config/
     │       │   └── SecurityConfig.java
     │       ├── controller/
     │       │   └── DemoController.java
     │       └── DemoApplication.java
     └── resources/
         └── application.yml
```

---

## ▶️ Como Rodar a Aplicação

Certifique-se de que o Keycloak está rodando localmente e configure corretamente o `issuer-uri` no `application.yml`.

Compile e rode a aplicação:

```bash
./mvnw spring-boot:run
```

A aplicação estará disponível em:

```
http://localhost:8083
```

---

## 🔑 Como Obter o Token JWT

Use Postman ou curl:

```bash
curl --location --request POST 'http://localhost:8080/realms/demo/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=spring-api' \
--data-urlencode 'client_secret=<SEU_CLIENT_SECRET>' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'username=user_demo' \
--data-urlencode 'password=<SENHA_USUARIO>'
```

A resposta conterá algo assim:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 300,
  "refresh_expires_in": 1800
}
```

Copie o valor do `access_token`.

---

## 🧪 Testando os Endpoints

Use o **Bearer Token** no cabeçalho das requisições:

```
Authorization: Bearer <seu_token_jwt>
```

### 🔓 Endpoint Público

```bash
curl http://localhost:8083/public
```

Resposta:

```
Acesso público
```

### 👤 Endpoint Autenticado

```bash
curl -H "Authorization: Bearer <token_user>" http://localhost:8083/user
```

Resposta:

```
Acesso autenticado
```

### 🔒 Endpoint Restrito (Admin)

```bash
curl -H "Authorization: Bearer <token_admin>" http://localhost:8083/admin
```

Resposta:

```
Acesso restrito a admins
```

---

## 🧰 Dicas Úteis

* Configure o JDK 17+ no IntelliJ ou VS Code.
* Para atualizar tokens, utilize o endpoint de refresh do Keycloak.
* Logs de autenticação podem ser verificados no console da aplicação Spring Boot.
