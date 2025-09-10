# BradServless - Sistema de Cadastro de Clientes

## Visão Geral

BradServless é uma aplicação moderna de cadastro de clientes desenvolvida com arquitetura 
serverless, composta por um frontend React.js e um backend Flask. O sistema oferece operações 
de um CRUD completo para gerenciamento de clientes com interface intuitiva e API REST robusta.

## Arquitetura

- **Frontend (BradServless-Front)**: React.js 18 com Vite
- **Backend (BradServless-Back)**: Flask com Python 3.9
- **Banco de Dados**: PostgreSQL (compatível com Amazon RDS)
- **Infraestrutura**: AWS Serverless (Lambda, API Gateway, RDS, etc.)

## Pré-requisitos

Antes de iniciar, certifique-se de ter instalado em seu sistema:

### Software Obrigatório

1. **Node.js** (versão 18 ou superior)
   - Download: https://nodejs.org/
   - Verificar instalação: `node --version`

2. **Python** (versão 3.9 ou superior)
   - Download: https://python.org/
   - Verificar instalação: `python --version`

3. **Git**
   - Download: https://git-scm.com/
   - Verificar instalação: `git --version`

4. **PostgreSQL** (versão 12 ou superior)
   - Download: https://postgresql.org/
   - Verificar instalação: `psql --version`

### Ferramentas Recomendadas

1. **IntelliJ IDEA** ou **Visual Studio Code**
2. **Postman** ou **Insomnia** (para testes de API)
3. **pgAdmin** (para gerenciamento do PostgreSQL)

## Configuração do Ambiente de Desenvolvimento

### 1. Preparação do Banco de Dados

#### 1.1 Instalação do PostgreSQL

**Windows:**
1. Baixe o instalador do PostgreSQL em https://postgresql.org/download/windows/
2. Execute o instalador e siga as instruções
3. Anote a senha do usuário `postgres` definida durante a instalação
4. Certifique-se de que o PostgreSQL está rodando como serviço

**Verificação da Instalação:**
```bash
# Abra o Command Prompt ou PowerShell
psql --version
```

#### 1.2 Criação do Banco de Dados

1. **Acesse o PostgreSQL:**
```bash
# No Command Prompt, conecte como usuário postgres
psql -U postgres -h localhost
```

2. **Crie o banco de dados:**
```sql
-- Criar o banco de dados
CREATE DATABASE "DB_Bradesco"
    WITH 
    OWNER = postgres
    ENCODING = 'UTF8'
    LC_COLLATE = 'Portuguese_Brazil.1252'
    LC_CTYPE = 'Portuguese_Brazil.1252'
    CONNECTION LIMIT = -1;

-- Conectar ao banco criado
\c "DB_Bradesco"

-- Habilitar extensão para UUIDs
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Criar a tabela de clientes
CREATE TABLE "Tb01-Cliente" (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    telefone VARCHAR(20) NOT NULL,
    data_criacao TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    data_atualizacao TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Verificar se a tabela foi criada
\dt
```

3. **Sair do PostgreSQL:**
```sql
\q
```

### 2. Configuração do Backend (BradServless-Back)

#### 2.1 Navegação e Preparação

1. **Extraia o arquivo ZIP** do backend em uma pasta de sua escolha
2. **Abra o Command Prompt** e navegue até a pasta do backend:
```bash
cd C:\caminho\para\bradservless-back
```

#### 2.2 Criação do Ambiente Virtual

```bash
# Criar ambiente virtual
python -m venv venv

# Ativar ambiente virtual (Windows)
venv\Scripts\activate

# Verificar se o ambiente está ativo (deve mostrar (venv) no prompt)
```

#### 2.3 Instalação de Dependências

```bash
# Instalar dependências
pip install -r requirements.txt

# Verificar instalação
pip list
```

#### 2.4 Configuração de Variáveis de Ambiente

1. **Crie um arquivo `.env`** na raiz do projeto backend:
```bash
# No Windows, use o Notepad ou seu editor preferido
notepad .env
```

2. **Adicione as seguintes configurações** no arquivo `.env`:
```env
# Configurações do Banco de Dados
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=DB_Bradesco
DATABASE_USER=postgres
DATABASE_PASSWORD=sua_senha_postgres_aqui

# Configurações da Aplicação
FLASK_ENV=development
FLASK_DEBUG=True
CORS_ORIGINS=http://localhost:5173

# Configurações AWS (para desenvolvimento local, deixe em branco)
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
```

#### 2.5 Teste do Backend

```bash
# Certifique-se de que o ambiente virtual está ativo
# Inicie o servidor Flask
python src/main.py
```

**Saída esperada:**
```
 * Serving Flask app 'main'
 * Debug mode: on
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://[::1]:5000
```

#### 2.6 Teste da API

Abra um novo Command Prompt e teste os endpoints:

```bash
# Teste de saúde da API
curl http://localhost:5000/api/health

# Teste de listagem de clientes (deve retornar lista vazia)
curl http://localhost:5000/api/clients
```

### 3. Configuração do Frontend (BradServless-Front)

#### 3.1 Preparação

1. **Extraia o arquivo ZIP** do frontend em uma pasta de sua escolha
2. **Abra um novo Command Prompt** e navegue até a pasta do frontend:
```bash
cd C:\caminho\para\bradservless-front
```

#### 3.2 Instalação de Dependências

```bash
# Instalar dependências usando npm
npm install

# Ou usando yarn (se preferir)
yarn install
```

#### 3.3 Configuração de Variáveis de Ambiente

1. **Crie um arquivo `.env`** na raiz do projeto frontend:
```bash
notepad .env
```

2. **Adicione a configuração** da API:
```env
# URL da API do Backend
VITE_API_BASE_URL=http://localhost:5000/api
```

#### 3.4 Inicialização do Frontend

```bash
# Iniciar servidor de desenvolvimento
npm run dev

# Ou usando yarn
yarn dev
```

**Saída esperada:**
```
  VITE v4.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h to show help
```

### 4. Verificação da Integração Completa

#### 4.1 Acesso à Aplicação

1. **Abra seu navegador** e acesse: http://localhost:5173/
2. **Verifique se a página carrega** sem erros
3. **Teste o cadastro de um cliente:**
   - Clique em "Novo Cliente"
   - Preencha os campos obrigatórios
   - Clique em "Salvar"
   - Verifique se o cliente aparece na lista

#### 4.2 Teste de Funcionalidades

**Teste de Criação:**
- Preencha o formulário com dados válidos
- Verifique se o cliente é salvo e aparece na lista

**Teste de Listagem:**
- Clique em "Atualizar Lista"
- Verifique se todos os clientes são exibidos

**Teste de Edição:**
- Clique no ícone de editar (lápis) de um cliente
- Modifique os dados e salve
- Verifique se as alterações foram aplicadas

**Teste de Exclusão:**
- Clique no ícone de excluir (lixeira) de um cliente
- Confirme a exclusão
- Verifique se o cliente foi removido da lista

## Estrutura dos Projetos

### Frontend (BradServless-Front)
```
bradservless-front/
├── public/
│   └── index.html
├── src/
│   ├── components/
│   │   ├── ClientForm.jsx
│   │   └── ClientList.jsx
│   ├── models/
│   │   └── Client.js
│   ├── pages/
│   │   └── HomePage.jsx
│   ├── services/
│   │   └── clientService.js
│   ├── App.jsx
│   ├── App.css
│   └── main.jsx
├── .env
├── .env.example
├── package.json
└── vite.config.js
```

### Backend (BradServless-Back)
```
bradservless-back/
├── src/
│   ├── models/
│   │   └── client.py
│   ├── routes/
│   │   └── client.py
│   └── main.py
├── venv/
├── .env
├── requirements.txt
└── README.md
```

## Comandos Úteis

### Backend
```bash
# Ativar ambiente virtual
venv\Scripts\activate

# Instalar nova dependência
pip install nome_da_biblioteca

# Atualizar requirements.txt
pip freeze > requirements.txt

# Executar aplicação
python src/main.py

# Executar testes (quando implementados)
pytest
```

### Frontend
```bash
# Instalar nova dependência
npm install nome_da_biblioteca

# Executar em modo desenvolvimento
npm run dev

# Build para produção
npm run build

# Preview do build de produção
npm run preview

# Executar testes (quando implementados)
npm test
```

## Solução de Problemas Comuns

### Problema: "Comando não encontrado"

**Sintoma:** Erro ao executar `node`, `python`, ou `psql`

**Solução:**
1. Verifique se o software está instalado
2. Adicione o caminho do executável à variável PATH do Windows
3. Reinicie o Command Prompt após alterar o PATH

### Problema: Erro de conexão com banco de dados

**Sintoma:** Erro "connection refused" ou "authentication failed"

**Solução:**
1. Verifique se o PostgreSQL está rodando:
   ```bash
   # No Windows, verifique os serviços
   services.msc
   ```
2. Confirme as credenciais no arquivo `.env`
3. Teste a conexão manualmente:
   ```bash
   psql -U postgres -h localhost -d DB_Bradesco
   ```

### Problema: Erro de CORS no frontend

**Sintoma:** Erro "CORS policy" no console do navegador

**Solução:**
1. Verifique se o backend está rodando na porta 5000
2. Confirme a configuração CORS no backend
3. Verifique a URL da API no arquivo `.env` do frontend

### Problema: Porta já em uso

**Sintoma:** Erro "port already in use" ou "EADDRINUSE"

**Solução:**
1. **Para o backend (porta 5000):**
   ```bash
   # Encontrar processo usando a porta
   netstat -ano | findstr :5000
   
   # Finalizar processo (substitua PID pelo número encontrado)
   taskkill /PID numero_do_pid /F
   ```

2. **Para o frontend (porta 5173):**
   ```bash
   # Encontrar processo usando a porta
   netstat -ano | findstr :5173
   
   # Finalizar processo
   taskkill /PID numero_do_pid /F
   ```

### Problema: Dependências não instaladas

**Sintoma:** Erro "module not found" ou "package not found"

**Solução:**
1. **Backend:**
   ```bash
   # Ativar ambiente virtual
   venv\Scripts\activate
   
   # Reinstalar dependências
   pip install -r requirements.txt
   ```

2. **Frontend:**
   ```bash
   # Limpar cache e reinstalar
   npm cache clean --force
   rm -rf node_modules
   npm install
   ```

## Desenvolvimento e Extensões

### Adicionando Novas Funcionalidades

1. **Backend:** Adicione novas rotas em `src/routes/`
2. **Frontend:** Crie novos componentes em `src/components/`
3. **Banco de Dados:** Execute migrations para mudanças de schema

### Boas Práticas

1. **Sempre ative o ambiente virtual** antes de trabalhar no backend
2. **Mantenha os arquivos `.env` atualizados** mas nunca os commite no Git
3. **Teste localmente** antes de fazer deploy
4. **Use branches** para novas funcionalidades
5. **Documente mudanças** no código

## Deploy para Produção

### AWS Amplify (Frontend)
1. Conecte seu repositório Git ao AWS Amplify
2. Configure as variáveis de ambiente de produção
3. O deploy será automático a cada push

### AWS Lambda (Backend)
1. Use AWS SAM para deploy das funções Lambda
2. Configure API Gateway para rotear requisições
3. Configure RDS para o banco de dados de produção

## Suporte e Contato

Para dúvidas ou problemas:

1. **Verifique a documentação** neste README
2. **Consulte os logs** da aplicação para erros específicos
3. **Teste em ambiente limpo** para isolar problemas
4. **Documente o problema** com passos para reproduzir

**Autor:** Williams Melquíades

