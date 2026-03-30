# 📧 Notificação - Microserviço

## 📌 Visão Geral

O microserviço **Notificação** é responsável pelo envio de emails a partir de dados de tarefas.

Ele recebe informações de uma tarefa via requisição HTTP, processa um template HTML e realiza o envio do email utilizando SMTP.

O serviço é utilizado como componente auxiliar dentro da arquitetura de microserviços.

---

## 🧱 Tecnologias Utilizadas

* Java 21
* Spring Boot
* Spring Web
* Spring Mail (`JavaMailSender`)
* Thymeleaf
* Lombok

---

## 🗂️ Estrutura do Projeto

```id="s6c2ap"
com.dominick.notificacao
├── business
│   ├── EmailService
│   ├── dto
│   │   └── TarefasDTO
│   └── enums
│       └── StatusNotificacaoEnum
├── controller
│   └── EmailController
├── infrastructure
│   └── exceptions
│       └── EmailException
```

---

## 📬 Funcionamento

Fluxo implementado no projeto:

1. Recebe uma requisição HTTP contendo os dados da tarefa
2. Os dados são encapsulados em um `TarefasDTO`
3. O `EmailService`:

    * Cria um `MimeMessage`
    * Processa o template HTML com Thymeleaf
    * Injeta os dados da tarefa no template
    * Envia o email via `JavaMailSender`

---

## 📬 Endpoint

### 🔹 Enviar Email

```id="8mq9n1"
POST /email
```

### Request Body

```json id="qz0l2y"
{
  "id": "string",
  "nomeTarefa": "string",
  "descricao": "string",
  "dataCriacao": "dd-MM-yyyy HH:mm:ss",
  "dataEvento": "dd-MM-yyyy HH:mm:ss",
  "emailUsuario": "string",
  "dataAteracao": "dd-MM-yyyy HH:mm:ss",
  "statusNotificacaoEnum": "PENDENTE"
}
```

### Response

* Status: `200 OK`
* Corpo: vazio (`ResponseEntity<Void>`)

---

## 📄 Template de Email

Arquivo:

```id="fc1r5z"
src/main/resources/templates/notificacao.html
```

### Variáveis utilizadas no template

* `nomeTarefa`
* `descricao`
* `dataEvento`

O processamento é feito via:

```java id="q1u8mb"
templateEngine.process("notificacao", context);
```

---

## 📦 DTO

Classe: `TarefasDTO`

### Características

* Utiliza formatação de datas:

```java id="yx9s3g"
@JsonFormat(pattern = "dd-MM-yyyy HH:mm:ss")
```

* Contém:

    * Dados da tarefa
    * Email do usuário
    * Status da notificação

---

## ⚙️ Serviço de Email

Classe: `EmailService`

### Responsabilidades implementadas

* Criar mensagem (`MimeMessage`)
* Definir remetente e destinatário
* Montar conteúdo HTML com Thymeleaf
* Enviar email via `JavaMailSender`

### Configuração utilizada no código

```java id="x3d7pa"
@Value("${envio.email.remetente}")
@Value("${envio.email.nomeRemetente}")
```

---

## ⚠️ Tratamento de Erro

Classe existente:

* `EmailException`

### Comportamento

* Lançada em falhas no envio de email
* Baseada em exceções como:

    * `MessagingException`
    * `UnsupportedEncodingException`

### Limitação atual

* Não existe `@ControllerAdvice`
* Não há tratamento global de exceções
* Respostas de erro não são padronizadas

---

## 🔐 Segurança

O microserviço **não possui implementação de segurança**:

* Não utiliza Spring Security
* Não valida JWT
* Endpoint `/email` é público

Não há no código qualquer mecanismo de autenticação ou autorização.

---

## ⚙️ Configuração

Arquivo de configuração:

```yaml id="0d5xk1"
server:
  port: 8085

spring:
  mail:
    host: smtp.gmail.com
    port: 587
    username: <email>
    password: <senha>
    properties:
      mail.smtp.auth: true
      mail.smtp.starttls.enable: true

envio:
  email:
    remetente: <email>
    nomeRemetente: <nome>
```

---

## 🐳 Docker

O projeto possui **Dockerfile** para containerização.

### Build da imagem

```bash id="m3z1xt"
docker build -t notificacao .
```

### Execução do container

```bash id="w8d2qp"
docker run -p 8085:8085 notificacao
```

---

## 🧪 Testes

Existe apenas:

* Teste de contexto (`contextLoads`)

Não há testes de:

* Regra de negócio
* Envio de email
* Controller

---

## 🚀 Como Executar

```bash id="n7a9cl"
git clone <repo>
cd notificacao
./gradlew bootRun
```

---

## 🚀 Melhorias Sugeridas (Baseadas no Código Real)

1. **Implementar tratamento global de exceções**

    * Criar `@ControllerAdvice`
    * Padronizar respostas de erro

2. **Adicionar segurança ao endpoint**

    * Validar JWT ou restringir acesso
    * Evitar exposição pública do envio de email

3. **Externalizar credenciais sensíveis**

    * Utilizar variáveis de ambiente
    * Remover credenciais do arquivo de configuração

4. **Criar testes automatizados**

    * Mock de `JavaMailSender`
    * Testes de envio de email
    * Testes de controller

---

## 👨‍💻 Autor

**Jhonatan Dominick**
