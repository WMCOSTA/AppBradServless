# Análise Técnica e Definição da Arquitetura

## Introdução
Este documento detalha a análise técnica e a proposta de arquitetura para a aplicação de cadastro de clientes, "BradServless-Front" e "BradServless-Back", que será executada em ambiente Windows e utilizará um banco de dados PostgreSQL. A solução será construída com foco em uma arquitetura serverless na AWS, aproveitando seus serviços para escalabilidade, resiliência e custo-benefício.

## Requisitos da Solicitação
Para garantir que todos os aspectos da solicitação sejam abordados, os requisitos foram categorizados e serão detalhados nas seções subsequentes:

1.  **Aplicação:** Cadastro de clientes em ambiente Windows.
2.  **Banco de Dados:** PostgreSQL.
3.  **Front-End (BradServless-Front):**
    *   Linguagem e comandos para layout.
    *   Ações CRUD (endpoints).
    *   Exibição de lista na tela inicial.
    *   Estrutura para rodar no AWS Amplify.
4.  **Back-End (BradServless-Back):**
    *   Linguagem/Framework e comandos.
    *   Arquitetura serverless orientada a eventos.
    *   Rodar em container na AWS.
    *   Funcionalidades de persistência de dados (CRUD).
5.  **Banco de Dados (DB_Bradesco):**
    *   Modelo PostgreSQL.
    *   Estrutura da tabela `Tb01-Cliente`.
6.  **Infraestrutura AWS (Etapa 4):**
    *   Amazon API Gateway.
    *   AWS Lambda ou Amazon ECS com AWS Fargate.
    *   Amazon SQS.
    *   Amazon SNS.
    *   Amazon RDS.
    *   Amazon CloudWatch e AWS X-Ray.
    *   AWS Identity and Access Management (IAM).
    *   AWS Systems Manager Parameter Store / Secrets Manager.
7.  **Desenho da Arquitetura:** Diagrama descrevendo a finalidade de cada recurso.
8.  **Especificação Técnica:** Detalhamento do padrão de arquitetura e recursos.
9.  **Arquivo Leia-me:** Roteiro passo a passo para configuração e execução em localhost.
10. **Entrega:** Módulos "BradServless-Front" e "BradServless-Back" em formato ZIP para importação no IntelliJ.

## Escolha de Tecnologias

### Front-End (BradServless-Front)
Para o front-end, considerando a necessidade de rodar no AWS Amplify e a criação de uma aplicação para ambiente Windows (que pode ser acessada via navegador), uma abordagem baseada em web é a mais adequada. Recomenda-se o uso de:

*   **Linguagem:** JavaScript/TypeScript.
*   **Framework:** React.js (com Next.js para SSR/SSG, se necessário, ou Create React App para SPA simples).
    *   **Justificativa:** React.js é amplamente suportado pelo AWS Amplify, oferece uma vasta comunidade, componentes reutilizáveis e uma excelente experiência de desenvolvimento para aplicações SPA (Single Page Application). O TypeScript adiciona tipagem estática, melhorando a manutenibilidade e a detecção de erros.

### Back-End (BradServless-Back)
Para o back-end serverless orientado a eventos e rodando em containers na AWS, as seguintes tecnologias são recomendadas:

*   **Linguagem:** Node.js (JavaScript/TypeScript) ou Python.
*   **Framework:** Express.js (para Node.js) ou Flask/FastAPI (para Python).
*   **Orquestração Serverless:** AWS Lambda (para funções serverless) ou AWS Fargate (para containers serverless).
    *   **Justificativa:** Node.js e Python são linguagens populares para desenvolvimento serverless, com forte suporte na AWS. Express.js, Flask e FastAPI são frameworks leves e eficientes para construir APIs RESTful. A escolha entre Lambda e Fargate dependerá da granularidade e do controle desejado sobre o ambiente de execução. Para microserviços orientados a eventos, o Lambda é geralmente a primeira escolha devido à sua natureza 


serverless e modelo de pagamento por uso. O Fargate é mais adequado para cargas de trabalho que exigem mais controle sobre o ambiente de contêiner ou que têm tempos de inicialização mais longos.

### Banco de Dados

*   **Sistema de Gerenciamento de Banco de Dados (SGBD):** PostgreSQL.
    *   **Justificativa:** O PostgreSQL é um SGBD relacional robusto, de código aberto e amplamente utilizado, que oferece alta confiabilidade, integridade de dados e um rico conjunto de recursos. É totalmente compatível com o Amazon RDS, o que simplifica a administração e a escalabilidade.

## Arquitetura Proposta (Visão Geral)

A arquitetura proposta seguirá um modelo serverless e orientado a eventos, utilizando os serviços da AWS para cada componente da aplicação. A comunicação entre o front-end e o back-end será realizada via API Gateway, que roteará as requisições para as funções Lambda ou contêineres Fargate. O SQS e o SNS serão utilizados para comunicação assíncrona e notificações, respectivamente. O RDS será o serviço de banco de dados para o PostgreSQL. CloudWatch e X-Ray serão empregados para monitoramento e rastreamento, enquanto IAM e Parameter Store/Secrets Manager garantirão a segurança e o gerenciamento de credenciais.

### Fluxo de Dados Simplificado

1.  O usuário interage com o **Front-End (BradServless-Front)** no ambiente Windows.
2.  O Front-End envia requisições HTTP/HTTPS para o **Amazon API Gateway**.
3.  O API Gateway roteia as requisições para o **Back-End (BradServless-Back)**, que pode ser implementado com **AWS Lambda** ou **Amazon ECS com AWS Fargate**.
4.  O Back-End processa a requisição, interage com o **Amazon RDS (PostgreSQL)** para persistência de dados.
5.  Para operações assíncronas ou notificações, o Back-End pode interagir com **Amazon SQS** ou **Amazon SNS**.
6.  **Amazon CloudWatch** e **AWS X-Ray** monitoram e rastreiam as interações entre os serviços.
7.  **AWS IAM** gerencia as permissões de acesso entre os serviços.
8.  **AWS Systems Manager Parameter Store / Secrets Manager** armazena e gerencia configurações e credenciais de forma segura.

## Detalhamento das Etapas

### Etapa 1 – Criação do Front-End (BradServless-Front)

*   **Linguagem:** React.js com TypeScript.
*   **Ferramenta de Criação:** Create React App (para um SPA simples) ou Next.js (para SSR/SSG, se houver requisitos de SEO ou performance inicial).
*   **Hospedagem:** AWS Amplify.

**Passos para Criação do Layout e CRUD (Exemplo com Create React App):**

1.  **Instalar Node.js e npm/yarn:** Certifique-se de ter o Node.js (versão LTS recomendada) e um gerenciador de pacotes (npm ou yarn) instalados em seu ambiente Windows.
2.  **Criar o Projeto React:**
    ```bash
    npx create-react-app bradservless-front --template typescript
    cd bradservless-front
    ```
3.  **Estrutura de Pastas:** Organize o projeto com pastas para componentes, páginas, serviços (para chamadas de API) e modelos de dados.
    ```
    src/
    ├── components/
    │   ├── ClientForm.tsx
    │   └── ClientList.tsx
    ├── pages/
    │   ├── HomePage.tsx
    │   └── ClientDetailPage.tsx
    ├── services/
    │   └── clientService.ts
    ├── models/
    │   └── Client.ts
    └── App.tsx
    └── index.tsx
    ```
4.  **Definir Modelos de Dados (models/Client.ts):**
    ```typescript
    export interface Client {
        id?: string;
        nome: string;
        email: string;
        telefone: string;
    }
    ```
5.  **Criar Serviço de API (services/clientService.ts):**
    Este serviço será responsável por interagir com o Back-End via API Gateway. Usaremos `axios` para as requisições HTTP.
    ```bash
    npm install axios
    ```
    ```typescript
    import axios from 'axios';
    import { Client } from '../models/Client';

    const API_BASE_URL = 'SUA_URL_DO_API_GATEWAY'; // Será configurada após a criação do Back-End

    export const clientService = {
        getAllClients: async (): Promise<Client[]> => {
            const response = await axios.get(`${API_BASE_URL}/clients`);
            return response.data;
        },
        getClientById: async (id: string): Promise<Client> => {
            const response = await axios.get(`${API_BASE_URL}/clients/${id}`);
            return response.data;
        },
        createClient: async (client: Client): Promise<Client> => {
            const response = await axios.post(`${API_BASE_URL}/clients`, client);
            return response.data;
        },
        updateClient: async (id: string, client: Client): Promise<Client> => {
            const response = await axios.put(`${API_BASE_URL}/clients/${id}`, client);
            return response.data;
        },
        deleteClient: async (id: string): Promise<void> => {
            await axios.delete(`${API_BASE_URL}/clients/${id}`);
        },
    };
    ```
6.  **Desenvolver Componentes React:**
    *   **ClientForm.tsx:** Componente para adicionar e editar clientes.
    *   **ClientList.tsx:** Componente para exibir a lista de clientes.
7.  **Páginas (pages/HomePage.tsx):**
    A página inicial exibirá a lista de clientes e um formulário para adicionar novos.
    ```typescript
    import React, { useEffect, useState } from 'react';
    import { Client } from '../models/Client';
    import { clientService } from '../services/clientService';
    import ClientList from '../components/ClientList';
    import ClientForm from '../components/ClientForm';

    const HomePage: React.FC = () => {
        const [clients, setClients] = useState<Client[]>([]);
        const [editingClient, setEditingClient] = useState<Client | null>(null);

        const fetchClients = async () => {
            const data = await clientService.getAllClients();
            setClients(data);
        };

        useEffect(() => {
            fetchClients();
        }, []);

        const handleSave = async (client: Client) => {
            if (editingClient) {
                await clientService.updateClient(editingClient.id!, client);
            } else {
                await clientService.createClient(client);
            }
            setEditingClient(null);
            fetchClients();
        };

        const handleEdit = (client: Client) => {
            setEditingClient(client);
        };

        const handleDelete = async (id: string) => {
            await clientService.deleteClient(id);
            fetchClients();
        };

        return (
            <div>
                <h1>Cadastro de Clientes</h1>
                <ClientForm client={editingClient} onSave={handleSave} />
                <ClientList clients={clients} onEdit={handleEdit} onDelete={handleDelete} />
            </div>
        );
    };

    export default HomePage;
    ```
8.  **Configurar Rotas (App.tsx):** Se houver mais de uma página, use `react-router-dom`.
    ```bash
    npm install react-router-dom
    ```
    ```typescript
    import React from 'react';
    import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';
    import HomePage from './pages/HomePage';

    const App: React.FC = () => {
        return (
            <Router>
                <Routes>
                    <Route path="/" element={<HomePage />} />
                    {/* Adicione outras rotas aqui, se necessário */}
                </Routes>
            </Router>
        );
    };

    export default App;
    ```
9.  **Rodar Localmente:**
    ```bash
    npm start
    ```
    Isso iniciará o servidor de desenvolvimento e abrirá a aplicação no navegador.

10. **Configurar para AWS Amplify:**
    Após o desenvolvimento, a aplicação React pode ser facilmente implantada no AWS Amplify. Os passos básicos são:
    *   Conectar seu repositório Git (GitHub, GitLab, Bitbucket, CodeCommit) ao Amplify.
    *   O Amplify detectará automaticamente o framework React e configurará o processo de build.
    *   Configurar variáveis de ambiente (como `API_BASE_URL`).
    *   O Amplify gerenciará o processo de CI/CD, implantando automaticamente as atualizações do seu código.

### Etapa 2 – Criação do Back-End (BradServless-Back)

*   **Linguagem/Framework:** Node.js com Express.js (para a API) e integração com AWS Lambda.
*   **Orquestração:** AWS Lambda (para a lógica de negócios) e Amazon API Gateway (para expor a API).
*   **Ferramenta de Desenvolvimento Serverless:** Serverless Framework ou AWS SAM (Serverless Application Model).
    *   **Justificativa:** O Serverless Framework é uma ferramenta poderosa e agnóstica de provedor de nuvem que simplifica o desenvolvimento, a implantação e o gerenciamento de aplicações serverless. Ele abstrai grande parte da complexidade da configuração da infraestrutura AWS.

**Passos para Criar o Back-End (Exemplo com Serverless Framework e Node.js):**

1.  **Instalar Serverless Framework:**
    ```bash
    npm install -g serverless
    ```
2.  **Configurar Credenciais AWS:** Certifique-se de que suas credenciais AWS estejam configuradas (via AWS CLI ou variáveis de ambiente) para que o Serverless Framework possa implantar recursos.
3.  **Criar um Projeto Serverless:**
    ```bash
    serverless create --template aws-nodejs-typescript --path bradservless-back
    cd bradservless-back
    ```
    Isso criará um projeto básico com um arquivo `serverless.yml` e um `handler.ts`.
4.  **Estrutura de Pastas:** Organize o projeto para separar a lógica de negócios, modelos e configurações.
    ```
    bradservless-back/
    ├── src/
    │   ├── handlers/
    │   │   └── clientHandler.ts
    │   ├── models/
    │   │   └── Client.ts
    │   └── services/
    │       └── clientService.ts
    ├── serverless.yml
    ├── package.json
    ├── tsconfig.json
    └── .env (para variáveis de ambiente locais)
    ```
5.  **Definir Modelo de Dados (src/models/Client.ts):** (Mesmo modelo do Front-End)
    ```typescript
    export interface Client {
        id?: string;
        nome: string;
        email: string;
        telefone: string;
    }
    ```
6.  **Configurar `serverless.yml`:** Este arquivo define sua aplicação serverless, incluindo funções Lambda, eventos (API Gateway), recursos (RDS, SQS, SNS, etc.) e permissões IAM.
    ```yaml
    service: bradservless-back

    frameworkVersion: '3'

    provider:
      name: aws
      runtime: nodejs18.x
      region: us-east-1 # Escolha sua região
      stage: dev
      environment:
        DATABASE_URL: ${ssm:/${self:service}/${sls:stage}/database-url} # Usar Parameter Store
        # Outras variáveis de ambiente, como nomes de filas SQS, tópicos SNS
      iamRoleStatements:
        - Effect: Allow
          Action:
            - rds-data:*
            - secretsmanager:GetSecretValue
            - ssm:GetParameter
            - sqs:SendMessage
            - sns:Publish
          Resource: "*" # Restringir para recursos específicos em produção

    functions:
      createClient:
        handler: src/handlers/clientHandler.createClient
        events:
          - http:
              path: clients
              method: post
              cors: true
      getAllClients:
        handler: src/handlers/clientHandler.getAllClients
        events:
          - http:
              path: clients
              method: get
              cors: true
      getClientById:
        handler: src/handlers/clientHandler.getClientById
        events:
          - http:
              path: clients/{id}
              method: get
              cors: true
      updateClient:
        handler: src/handlers/clientHandler.updateClient
        events:
          - http:
              path: clients/{id}
              method: put
              cors: true
      deleteClient:
        handler: src/handlers/clientHandler.deleteClient
        events:
          - http:
              path: clients/{id}
              method: delete
              cors: true

    plugins:
      - serverless-offline # Para desenvolvimento local
      - serverless-dotenv-plugin # Para carregar variáveis de ambiente de .env
      - serverless-plugin-typescript # Para compilar TypeScript

    custom:
      dotenv:
        path: ./.env
    ```
7.  **Implementar Handlers (src/handlers/clientHandler.ts):**
    Estes serão os pontos de entrada para suas funções Lambda. Eles receberão as requisições do API Gateway e chamarão a lógica de negócios.
    ```typescript
    import { APIGatewayProxyEvent, APIGatewayProxyResult } from 'aws-lambda';
    import { Client } from '../models/Client';
    import { clientService } from '../services/clientService';

    export const createClient = async (event: APIGatewayProxyEvent): Promise<APIGatewayProxyResult> => {
        try {
            const client: Client = JSON.parse(event.body || '{}');
            const newClient = await clientService.createClient(client);
            return {
                statusCode: 201,
                body: JSON.stringify(newClient),
                headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
            };
        } catch (error) {
            console.error(error);
            return {
                statusCode: 500,
                body: JSON.stringify({ message: 'Erro ao criar cliente' }),
                headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
            };
        }
    };

    export const getAllClients = async (event: APIGatewayProxyEvent): Promise<APIGatewayProxyResult> => {
        try {
            const clients = await clientService.getAllClients();
            return {
                statusCode: 200,
                body: JSON.stringify(clients),
                headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
            };
        } catch (error) {
            console.error(error);
            return {
                statusCode: 500,
                body: JSON.stringify({ message: 'Erro ao buscar clientes' }),
                headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
            };
        }
    };

    // Implementar getClientById, updateClient, deleteClient de forma similar
    ```
8.  **Implementar Lógica de Negócios e Persistência (src/services/clientService.ts):**
    Este serviço conterá a lógica para interagir com o banco de dados PostgreSQL. Usaremos um cliente PostgreSQL para Node.js, como `pg` ou `typeorm`.
    ```bash
    npm install pg
    npm install @types/pg # Para TypeScript
    ```
    ```typescript
    import { Client } from '../models/Client';
    import { Pool } from 'pg';

    const pool = new Pool({
        connectionString: process.env.DATABASE_URL,
    });

    export const clientService = {
        createClient: async (client: Client): Promise<Client> => {
            const result = await pool.query(
                'INSERT INTO "Tb01-Cliente" (nome, email, telefone) VALUES ($1, $2, $3) RETURNING *',
                [client.nome, client.email, client.telefone]
            );
            return result.rows[0];
        },
        getAllClients: async (): Promise<Client[]> => {
            const result = await pool.query('SELECT * FROM "Tb01-Cliente"');
            return result.rows;
        },
        // Implementar getClientById, updateClient, deleteClient
    };
    ```
9.  **Desenvolvimento Local:**
    Para testar o back-end localmente, use o plugin `serverless-offline`.
    ```bash
    serverless offline start
    ```
    Isso emulará o API Gateway e as funções Lambda localmente, permitindo que você teste sua API antes de implantar.

10. **Implantação na AWS:**
    ```bash
    serverless deploy
    ```
    Este comando implantará sua aplicação serverless na AWS, criando os recursos definidos em `serverless.yml` (funções Lambda, API Gateway endpoints, etc.).

### Etapa 3 – Crie o banco de dados (DB_Bradesco) usando o modelo do Postgress e defina a estrutura da tabela (Tb01-Cliente) para cadastrar os dados dos clientes no banco de dados.

**Criação do Banco de Dados (Amazon RDS PostgreSQL):**

1.  **Acessar o Console AWS:** Navegue até o serviço Amazon RDS.
2.  **Criar Banco de Dados:** Clique em "Create database".
3.  **Escolher Mecanismo:** Selecione "PostgreSQL".
4.  **Configurações de Modelo:** Escolha o modelo de implantação (e.g., "Free tier" para testes, "Production" para ambientes reais).
5.  **Configurações de Conectividade:**
    *   **VPC:** Selecione a VPC onde seus recursos Lambda/Fargate estarão.
    *   **Subnet group:** Crie ou selecione um grupo de sub-redes.
    *   **Public access:** "No" (recomendado para segurança, acesso via VPC).
    *   **VPC security group:** Crie um novo Security Group que permita o tráfego de entrada da sua VPC (onde as Lambdas/Fargate estarão) na porta 5432 (padrão PostgreSQL).
6.  **Credenciais:** Defina o nome de usuário mestre e a senha.
7.  **Nome do Banco de Dados Inicial:** Opcional, mas pode ser `DB_Bradesco`.
8.  **Criar Banco de Dados:** Revise e crie o banco de dados.

**Estrutura da Tabela (Tb01-Cliente):**

Após a criação da instância RDS, você pode se conectar a ela usando um cliente PostgreSQL (como `psql`, DBeaver, pgAdmin) e executar o seguinte comando SQL para criar a tabela `Tb01-Cliente`:

```sql
CREATE TABLE "Tb01-Cliente" (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    telefone VARCHAR(20)
);
```

*   **`id UUID PRIMARY KEY DEFAULT gen_random_uuid()`:** Define uma chave primária `id` do tipo UUID (Universally Unique Identifier) que será gerada automaticamente. Isso é útil para evitar colisões de IDs em sistemas distribuídos.
*   **`nome VARCHAR(255) NOT NULL`:** Campo para o nome do cliente, com limite de 255 caracteres e não pode ser nulo.
*   **`email VARCHAR(255) UNIQUE NOT NULL`:** Campo para o e-mail do cliente, com limite de 255 caracteres, deve ser único e não pode ser nulo.
*   **`telefone VARCHAR(20)`:** Campo para o telefone do cliente, com limite de 20 caracteres.

### Etapa 4 – Descreva passo a passo como criar cada recurso ou serviço na infraestrutura da AWS para rodar a aplicação conforme segue abaixo:

Esta etapa detalha a configuração dos serviços AWS necessários. Muitos desses recursos serão provisionados automaticamente pelo Serverless Framework (se usado para o Back-End) ou pelo AWS Amplify (para o Front-End), mas é importante entender o papel de cada um.

1.  **Amazon API Gateway:**
    *   **Finalidade:** Atua como o "porteiro" para sua aplicação, recebendo todas as requisições HTTP/HTTPS do Front-End e roteando-as para o Back-End (Lambda ou Fargate).
    *   **Criação:** Geralmente provisionado automaticamente pelo Serverless Framework quando você define eventos `http` em suas funções Lambda. Você também pode criá-lo manualmente no console do API Gateway, definindo recursos, métodos (GET, POST, PUT, DELETE) e integrações (Lambda Proxy Integration).
    *   **Configuração Essencial:** Habilitar CORS (Cross-Origin Resource Sharing) para permitir que o Front-End (hospedado em um domínio diferente) acesse a API.

2.  **Permita configurar os recursos para o código rodar em:**

    *   **AWS Lambda (Serverless):**
        *   **Finalidade:** Executa seu código de Back-End em resposta a eventos (como requisições do API Gateway) sem a necessidade de provisionar ou gerenciar servidores. Ideal para microserviços e funções de curta duração.
        *   **Criação:** No console Lambda, você pode criar funções, definir o runtime (Node.js, Python, etc.), carregar seu código e configurar gatilhos (triggers) como API Gateway. O Serverless Framework automatiza isso via `serverless.yml`.
        *   **Configuração Essencial:** Definir memória, tempo limite (timeout), variáveis de ambiente (e.g., `DATABASE_URL`), e uma Role IAM com permissões para acessar outros serviços AWS (RDS, SQS, SNS, Parameter Store, Secrets Manager).

    *   **Ou Amazon ECS com AWS Fargate (Contêineres):**
        *   **Finalidade:** Permite executar contêineres Docker sem gerenciar a infraestrutura subjacente (servidores EC2). Oferece mais controle sobre o ambiente de execução do que o Lambda, sendo adequado para aplicações com requisitos de recursos mais consistentes ou que precisam de tempos de inicialização mais rápidos.
        *   **Criação:**
            1.  **Criar um Cluster ECS:** No console ECS, crie um cluster do tipo Fargate.
            2.  **Criar uma Definição de Tarefa (Task Definition):** Defina a imagem Docker do seu Back-End, portas, variáveis de ambiente, limites de CPU/memória e a Role IAM para a tarefa.
            3.  **Criar um Serviço ECS:** Associe a Definição de Tarefa a um serviço, que gerenciará a execução e a escalabilidade das suas tarefas no cluster. Configure um Load Balancer (Application Load Balancer - ALB) para distribuir o tráfego para suas tarefas.
            4.  **Configuração Essencial:** Construir a imagem Docker do seu Back-End e publicá-la no Amazon ECR (Elastic Container Registry). Configurar o ALB para rotear o tráfego para o serviço ECS e garantir que o Security Group do ALB permita o tráfego de entrada na porta HTTP/HTTPS.

3.  **Amazon SQS (Simple Queue Service):**
    *   **Finalidade:** Serviço de fila de mensagens totalmente gerenciado para desacoplar e escalar microserviços, sistemas distribuídos e aplicações serverless. Permite comunicação assíncrona.
    *   **Criação:** No console SQS, crie uma fila (Standard ou FIFO). Você pode configurar políticas de acesso para permitir que suas funções Lambda ou tarefas Fargate enviem/recebam mensagens.
    *   **Exemplo de Uso:** Enviar mensagens para uma fila SQS para processamento em segundo plano (e.g., envio de e-mails de boas-vindas, processamento de dados).

4.  **Amazon SNS (Simple Notification Service):**
    *   **Finalidade:** Serviço de notificação totalmente gerenciado que permite enviar mensagens para um grande número de assinantes (e.g., e-mail, SMS, outras funções Lambda, filas SQS).
    *   **Criação:** No console SNS, crie um tópico. Você pode configurar assinaturas para o tópico (e.g., um endereço de e-mail, uma função Lambda que processa notificações).
    *   **Exemplo de Uso:** Notificar administradores sobre novos cadastros de clientes ou erros críticos na aplicação.

5.  **Amazon RDS (Relational Database Service):**
    *   **Finalidade:** Serviço de banco de dados relacional gerenciado que facilita a configuração, operação e escalabilidade de bancos de dados relacionais na nuvem. Suporta PostgreSQL.
    *   **Criação:** (Já detalhado na Etapa 3).
    *   **Configuração Essencial:** Garantir que o Security Group do RDS permita o tráfego de entrada das suas funções Lambda/Fargate (na porta 5432) e que a instância RDS esteja na mesma VPC ou em uma VPC acessível.

6.  **Amazon CloudWatch e AWS X-Ray:**
    *   **Amazon CloudWatch:**
        *   **Finalidade:** Serviço de monitoramento e observabilidade que fornece dados e insights acionáveis para monitorar suas aplicações, responder a alterações de desempenho em todo o sistema e otimizar a utilização de recursos.
        *   **Uso:** Coleta logs de funções Lambda, métricas de API Gateway, RDS, etc. Você pode criar alarmes para notificar sobre anomalias (e.g., alta latência da API, erros de banco de dados).
    *   **AWS X-Ray:**
        *   **Finalidade:** Ajuda desenvolvedores a analisar e depurar aplicações distribuídas, como as criadas com microsserviços. Fornece uma visão de ponta a ponta das requisições à medida que elas viajam pelos componentes da sua aplicação.
        *   **Uso:** Integrado com Lambda e API Gateway. Habilite o X-Ray para suas funções Lambda e API Gateway para rastrear as requisições e identificar gargalos de desempenho.

7.  **AWS Identity and Access Management (IAM):**
    *   **Finalidade:** Gerencia o acesso seguro aos serviços e recursos da AWS. Permite criar e gerenciar usuários e grupos da AWS e usar permissões para permitir e negar o acesso a recursos da AWS.
    *   **Uso:** Criar Roles IAM para suas funções Lambda e tarefas Fargate, concedendo apenas as permissões mínimas necessárias (princípio do menor privilégio) para acessar o RDS, SQS, SNS, Parameter Store, Secrets Manager, etc.

8.  **AWS Systems Manager Parameter Store / Secrets Manager:**
    *   **AWS Systems Manager Parameter Store:**
        *   **Finalidade:** Fornece armazenamento seguro e hierárquico para dados de configuração e gerenciamento de segredos. Você pode armazenar dados como senhas de banco de dados, strings de conexão e chaves de API como valores de parâmetros.
        *   **Uso:** Armazenar a string de conexão do banco de dados (`DATABASE_URL`) e outras configurações que não são segredos sensíveis. Pode ser referenciado diretamente no `serverless.yml`.
    *   **AWS Secrets Manager:**
        *   **Finalidade:** Ajuda a proteger os segredos necessários para acessar suas aplicações, serviços e recursos. Permite girar, gerenciar e recuperar credenciais de banco de dados, chaves de API e outros segredos durante todo o ciclo de vida.
        *   **Uso:** Armazenar segredos mais sensíveis, como senhas de banco de dados. É mais robusto para gerenciamento de segredos e rotação automática. Suas funções Lambda podem recuperar segredos do Secrets Manager em tempo de execução.

### Etapa 5 - Faça um desenho da arquitetura criada na infraestrutura da AWS descrevendo a finalidade de cada recurso ou serviço utilizado.

Esta etapa envolverá a criação de um diagrama de arquitetura. Será utilizado uma ferramenta para gerar o diagrama com base na descrição textual.

### Etapa 6 – Crie um arquivo de especificação técnica contendo todo detalhamento do padrão de arquitetura e recursos utilizados na criação do front-end “BradServless-Front” e Back-end “BradServless-Back”.

Esta etapa envolverá a compilação de todas as informações detalhadas nas etapas anteriores em um documento técnico formal.

### Etapa 7 – Crie um arquivo Leia-me contendo o roteiro passo a passo de como configura e rodar a aplicação no meu ambiente localhost.

Esta etapa envolverá a criação de um guia prático para o usuário.

### Etapa 8 – Entregue os módulos do “BradServless-Front” e “BradServless-Back” no formato zip, de forma que eu possa importar na IDE do InteliJ.

Esta etapa final envolverá o empacotamento dos projetos de Front-End e Back-End em arquivos ZIP para entrega.

