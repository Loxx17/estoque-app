# EstoqueApp — Sistema de Controle de Estoque

Sistema distribuído de controle de estoque para pequenos comércios, desenvolvido em Java com Spring Boot (backend) e React com Vite (frontend).

---

## Tecnologias

| Camada | Tecnologia | Versão |
|--------|-----------|--------|
| Backend | Java + Spring Boot | 17 / 3.2 |
| Banco de dados | PostgreSQL | 18 |
| Frontend | React + Vite | 18 / 5 |
| Autenticação | Spring Security + JWT | — |
| ORM | Spring Data JPA + Hibernate | — |

---

## Pré-requisitos

Instale nessa ordem antes de qualquer coisa:

1. **Java 17 (JDK)** → https://adoptium.net  
   Baixe: `JDK 17 Windows x64`

2. **PostgreSQL 18** → https://postgresql.org/download/windows  
   Durante a instalação, defina uma senha para o usuário `postgres` e **anote ela**

3. **Node.js 18 LTS** → https://nodejs.org/en/download/releases  
   Baixe: `Node.js 18.20.4 Windows x64`

4. **IntelliJ IDEA Community** → https://jetbrains.com/idea/download  
   Baixe a versão **Community** (gratuita)

---

## Estrutura do projeto

```
projeto/
├── estoque-api/          ← Backend Spring Boot (porta 8080)
└── estoque-frontend/     ← Frontend React + Vite (porta 3000)
```

---

## 1. Configurar o banco de dados

### 1.1 Iniciar o PostgreSQL

Abra o **Prompt de Comando como Administrador** e execute:

```
net start postgresql-x64-18
```

> Se aparecer "The service has already been started", está tudo certo — já estava rodando.

### 1.2 Criar o banco

1. Abra o **pgAdmin 4** (instalado junto com o PostgreSQL)
2. No painel esquerdo: **Servers → PostgreSQL 18** → digite sua senha
3. Clique com botão direito em **Databases → Create → Database**
4. Digite `estoque_db` no campo nome e clique em **Save**

### 1.3 Criar o usuário administrador

1. Clique com botão direito em **estoque_db → Query Tool**
2. Cole e execute o SQL abaixo (pressione **F5** ou clique no botão ▶):

```sql
INSERT INTO usuarios (nome, email, senha, role)
VALUES (
  'Administrador',
  'admin@estoque.com',
  '$2a$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi',
  'ADMIN'
);
```

> Este passo só é necessário na **primeira vez**. Nas próximas execuções o usuário já estará no banco.

---

## 2. Configurar e rodar o backend

### 2.1 Abrir no IntelliJ

1. Abra o **IntelliJ IDEA**
2. Clique em **File → Open** e selecione a pasta `estoque-api`
3. Aguarde o Maven baixar as dependências (barra de progresso no rodapé)

### 2.2 Configurar a senha do banco

Abra o arquivo `src/main/resources/application.yml` e edite:

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/estoque_db
    username: postgres
    password: SUA_SENHA_AQUI   # ← substitua pela sua senha do PostgreSQL
```

### 2.3 Executar

1. Abra o arquivo `src/main/java/com/estoque/EstoqueApplication.java`
2. Clique na seta verde ▶ ao lado do método `main`
3. Aguarde a mensagem no log:

```
Started EstoqueApplication in X seconds
```

> O Hibernate criará as tabelas automaticamente na primeira execução.

A API estará disponível em: `http://localhost:8080`

---

## 3. Rodar o frontend

Abra um novo **Prompt de Comando** (pode ser sem ser Administrador) e execute:

```bash
# Navegue até a pasta do frontend
cd caminho\para\estoque-frontend

# Instale as dependências (só na primeira vez)
npm install

# Inicie o servidor de desenvolvimento
npm start
```

O frontend estará disponível em: `http://localhost:3000`

> O `npm install` pode demorar alguns minutos na primeira vez.  
> Nas próximas execuções basta rodar `npm start` diretamente.

---

## 4. Acessar o sistema

Abra o navegador e acesse:

```
http://localhost:3000
```

**Credenciais padrão:**

| Campo | Valor |
|-------|-------|
| E-mail | admin@estoque.com |
| Senha | password |

---

## 5. Ordem de inicialização

Sempre siga essa ordem ao iniciar o sistema:

```
1. PostgreSQL    →    2. Backend (IntelliJ)    →    3. Frontend (npm start)
```

Se pular algum passo ou iniciar fora de ordem, podem ocorrer erros de conexão.

---

## 6. Funcionalidades do sistema

| Funcionalidade | Descrição |
|---------------|-----------|
| Login | Autenticação com e-mail e senha, token JWT |
| Dashboard | Visão geral com cards e gráfico de estoque |
| Produtos | Cadastro, edição, inativação e busca |
| Movimentações | Registro de entradas e saídas de estoque |
| Alertas | Lista de produtos abaixo do estoque mínimo |
| Categorias | Cadastro e gestão de categorias |
| Fornecedores | Cadastro e gestão de fornecedores |
| Perfis | ADMIN (acesso total) e OPERADOR (somente leitura + movimentações) |

---

## 7. Endpoints da API

A API roda em `http://localhost:8080`. Todas as rotas (exceto login) exigem o header:

```
Authorization: Bearer {token}
```

| Método | Rota | Descrição |
|--------|------|-----------|
| POST | /api/auth/login | Login e obtenção do token |
| POST | /api/auth/register | Cadastro de novo usuário |
| GET | /api/produtos | Listar produtos ativos |
| POST | /api/produtos | Cadastrar produto |
| PUT | /api/produtos/{id} | Atualizar produto |
| DELETE | /api/produtos/{id} | Inativar produto |
| GET | /api/produtos/estoque-baixo | Produtos com estoque baixo |
| GET | /api/categorias | Listar categorias |
| POST | /api/categorias | Cadastrar categoria |
| GET | /api/fornecedores | Listar fornecedores |
| POST | /api/fornecedores | Cadastrar fornecedor |
| POST | /api/movimentacoes | Registrar movimentação |
| GET | /api/movimentacoes/produto/{id} | Histórico por produto |

---

## 8. Solução de problemas comuns

**"Connection to localhost:5432 refused"**  
O PostgreSQL não está rodando. Abra o Prompt como Administrador e execute:
```
net start postgresql-x64-18
```

**"Port 8080 already in use"**  
Já tem uma instância do backend rodando. Pare o processo no IntelliJ (botão ■) e reinicie.

**"npm: command not found"**  
O Node.js não está instalado ou não foi adicionado ao PATH. Reinstale o Node.js 18.

**Frontend abre mas não carrega dados**  
Verifique se o backend está rodando em `http://localhost:8080`. O frontend depende da API para funcionar.

**"Bad credentials" no login**  
Execute o SQL da seção 1.3 novamente no pgAdmin para recriar o usuário administrador.

---

## 9. Informações do projeto

- **Disciplina:** Desenvolvimento de Aplicação Distribuída em Java
- **Professor:** Emerson Paduan
- **Arquitetura:** Cliente-Servidor com dois serviços independentes
- **Banco de dados:** Relacional (PostgreSQL) com 5 entidades
