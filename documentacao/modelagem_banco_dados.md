# Modelagem do Banco de Dados PostgreSQL

## Introdução

Este documento detalha a modelagem do banco de dados PostgreSQL para a aplicação BradServless, especificamente focado na estrutura da tabela `Tb01-Cliente` que será utilizada para armazenar os dados dos clientes cadastrados no sistema. A modelagem foi projetada considerando as melhores práticas de design de banco de dados, escalabilidade, performance e integridade dos dados.

## Visão Geral da Arquitetura de Dados

A aplicação BradServless utiliza uma arquitetura de dados simples e eficiente, centrada no conceito de cadastro de clientes. O banco de dados PostgreSQL foi escolhido como sistema de gerenciamento de banco de dados relacional devido às suas características robustas, confiabilidade e amplo suporte na plataforma AWS através do Amazon RDS.

### Características do PostgreSQL Escolhidas

O PostgreSQL oferece diversas vantagens que justificam sua escolha para este projeto. Primeiramente, é um sistema de banco de dados de código aberto com uma comunidade ativa e extensa documentação, garantindo suporte contínuo e atualizações regulares de segurança. Além disso, o PostgreSQL possui excelente compatibilidade com o Amazon RDS, permitindo configuração automatizada, backups gerenciados, patches de segurança automáticos e escalabilidade horizontal e vertical conforme necessário.

O sistema também oferece suporte nativo para tipos de dados UUID (Universally Unique Identifier), que são fundamentais para a arquitetura distribuída e serverless proposta. Os UUIDs garantem que os identificadores sejam únicos globalmente, evitando conflitos em sistemas distribuídos onde múltiplas instâncias podem estar criando registros simultaneamente.

## Estrutura do Banco de Dados

### Nome do Banco de Dados

O banco de dados será denominado `DB_Bradesco`, seguindo a convenção de nomenclatura especificada nos requisitos do projeto. Este nome reflete claramente o contexto empresarial e facilita a identificação em ambientes com múltiplos bancos de dados.

### Esquema de Dados

O esquema de dados seguirá o padrão público (public schema) do PostgreSQL, que é adequado para aplicações de pequeno a médio porte. Para aplicações maiores, seria recomendável criar esquemas específicos para diferentes módulos funcionais, mas para o escopo atual, o esquema público atende perfeitamente às necessidades.

## Tabela Tb01-Cliente

### Definição e Propósito

A tabela `Tb01-Cliente` é o componente central do modelo de dados, responsável por armazenar todas as informações relacionadas aos clientes cadastrados no sistema. O nome da tabela segue uma convenção que inclui um prefixo numérico (`Tb01`) que facilita a organização e ordenação em ambientes com múltiplas tabelas.

### Estrutura Detalhada da Tabela

A estrutura da tabela foi cuidadosamente projetada para atender aos requisitos funcionais da aplicação, garantindo integridade dos dados, performance adequada e facilidade de manutenção. Cada campo foi definido com tipos de dados apropriados e restrições que garantem a qualidade dos dados armazenados.

#### Campo ID (Chave Primária)

O campo `id` utiliza o tipo de dados UUID (Universally Unique Identifier) como chave primária. Esta escolha técnica oferece várias vantagens significativas em comparação com chaves primárias sequenciais tradicionais. Os UUIDs são gerados de forma aleatória e garantem unicidade global, eliminando a necessidade de coordenação entre diferentes instâncias da aplicação para gerar identificadores únicos.

A função `gen_random_uuid()` do PostgreSQL é utilizada como valor padrão, garantindo que cada novo registro receba automaticamente um identificador único no momento da inserção. Esta abordagem é particularmente vantajosa em arquiteturas serverless, onde múltiplas funções Lambda podem estar executando simultaneamente e criando registros no banco de dados.

#### Campo Nome

O campo `nome` é definido como `VARCHAR(255)` com restrição `NOT NULL`, garantindo que todo cliente cadastrado possua obrigatoriamente um nome. O limite de 255 caracteres é adequado para a maioria dos nomes completos, incluindo nomes compostos e sobrenomes múltiplos, comum em diferentes culturas e nacionalidades.

A escolha do tipo `VARCHAR` em vez de `CHAR` é intencional, pois permite armazenamento eficiente de nomes de diferentes tamanhos sem desperdício de espaço. O PostgreSQL otimiza automaticamente o armazenamento de campos `VARCHAR`, utilizando apenas o espaço necessário para cada valor.

#### Campo Email

O campo `email` é implementado como `VARCHAR(255)` com restrições `UNIQUE` e `NOT NULL`. A restrição de unicidade garante que não existam dois clientes com o mesmo endereço de email no sistema, o que é fundamental para a integridade dos dados e para funcionalidades como autenticação e comunicação.

O limite de 255 caracteres para emails é baseado nas especificações RFC 5321 e RFC 5322, que definem os padrões para endereços de email na internet. Embora a especificação técnica permita emails com até 320 caracteres, na prática, emails com mais de 255 caracteres são extremamente raros e podem causar problemas de compatibilidade com alguns sistemas.

#### Campo Telefone

O campo `telefone` é definido como `VARCHAR(20)` com restrição `NOT NULL`. A escolha de um campo de texto em vez de um tipo numérico é intencional, pois números de telefone frequentemente incluem caracteres especiais como parênteses, hífens, espaços e códigos de país com o símbolo de mais (+).

O limite de 20 caracteres é suficiente para acomodar a maioria dos formatos de telefone internacionais, incluindo códigos de país, códigos de área e números locais. Este tamanho permite flexibilidade na formatação dos números, facilitando a entrada de dados pelos usuários e a exibição formatada na interface.

### Script SQL de Criação

```sql
-- Criação do banco de dados DB_Bradesco
CREATE DATABASE "DB_Bradesco"
    WITH 
    OWNER = postgres
    ENCODING = 'UTF8'
    LC_COLLATE = 'pt_BR.UTF-8'
    LC_CTYPE = 'pt_BR.UTF-8'
    TABLESPACE = pg_default
    CONNECTION LIMIT = -1;

-- Conectar ao banco de dados DB_Bradesco
\c "DB_Bradesco"

-- Habilitar a extensão para geração de UUIDs
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Criação da tabela Tb01-Cliente
CREATE TABLE "Tb01-Cliente" (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    telefone VARCHAR(20) NOT NULL,
    data_criacao TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    data_atualizacao TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Comentários para documentação da tabela
COMMENT ON TABLE "Tb01-Cliente" IS 'Tabela principal para armazenamento de dados de clientes do sistema BradServless';
COMMENT ON COLUMN "Tb01-Cliente".id IS 'Identificador único do cliente (UUID)';
COMMENT ON COLUMN "Tb01-Cliente".nome IS 'Nome completo do cliente';
COMMENT ON COLUMN "Tb01-Cliente".email IS 'Endereço de email do cliente (único no sistema)';
COMMENT ON COLUMN "Tb01-Cliente".telefone IS 'Número de telefone do cliente';
COMMENT ON COLUMN "Tb01-Cliente".data_criacao IS 'Data e hora de criação do registro';
COMMENT ON COLUMN "Tb01-Cliente".data_atualizacao IS 'Data e hora da última atualização do registro';
```

### Campos Adicionais de Auditoria

Além dos campos básicos especificados nos requisitos, foram incluídos dois campos adicionais que são considerados boas práticas em sistemas de produção: `data_criacao` e `data_atualizacao`. Estes campos fornecem informações valiosas para auditoria, debugging e análise de dados.

O campo `data_criacao` registra automaticamente o momento exato em que cada registro foi inserido no banco de dados, utilizando o tipo `TIMESTAMP WITH TIME ZONE` para garantir que as informações de fuso horário sejam preservadas. Isto é particularmente importante em aplicações distribuídas que podem ser acessadas de diferentes regiões geográficas.

O campo `data_atualizacao` é atualizado automaticamente sempre que um registro é modificado, permitindo rastrear quando foram feitas as últimas alterações nos dados de cada cliente. Este campo é fundamental para implementar funcionalidades como sincronização de dados e detecção de conflitos em sistemas distribuídos.

## Índices e Performance

### Índices Automáticos

O PostgreSQL cria automaticamente índices para chaves primárias e restrições de unicidade. Portanto, os campos `id` e `email` da tabela `Tb01-Cliente` terão índices automáticos que garantem performance otimizada para consultas baseadas nestes campos.

### Índices Adicionais Recomendados

Para otimizar a performance de consultas específicas, podem ser criados índices adicionais conforme a aplicação evolui e os padrões de uso se tornam mais claros. Alguns índices que podem ser considerados incluem:

```sql
-- Índice para consultas por nome (busca parcial)
CREATE INDEX idx_cliente_nome ON "Tb01-Cliente" USING gin(to_tsvector('portuguese', nome));

-- Índice para consultas por data de criação
CREATE INDEX idx_cliente_data_criacao ON "Tb01-Cliente" (data_criacao);

-- Índice composto para consultas por nome e email
CREATE INDEX idx_cliente_nome_email ON "Tb01-Cliente" (nome, email);
```

O índice GIN (Generalized Inverted Index) no campo nome permite buscas de texto completo eficientes, utilizando o dicionário português para melhor precisão nas consultas. Este tipo de índice é particularmente útil para implementar funcionalidades de busca por nome parcial ou busca fuzzy.

## Restrições e Validações

### Restrições de Integridade

As restrições de integridade são fundamentais para garantir a qualidade e consistência dos dados armazenados. A tabela `Tb01-Cliente` implementa várias restrições que previnem a inserção de dados inválidos ou inconsistentes.

A restrição `NOT NULL` nos campos obrigatórios garante que informações essenciais sempre estejam presentes. A restrição `UNIQUE` no campo email previne duplicações que poderiam causar problemas funcionais na aplicação.

### Validações Adicionais

Para garantir maior qualidade dos dados, podem ser implementadas validações adicionais através de constraints check:

```sql
-- Validação de formato de email
ALTER TABLE "Tb01-Cliente" 
ADD CONSTRAINT chk_email_format 
CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');

-- Validação de comprimento mínimo do nome
ALTER TABLE "Tb01-Cliente" 
ADD CONSTRAINT chk_nome_length 
CHECK (LENGTH(TRIM(nome)) >= 2);

-- Validação de formato de telefone brasileiro
ALTER TABLE "Tb01-Cliente" 
ADD CONSTRAINT chk_telefone_format 
CHECK (telefone ~ '^\(\d{2}\)\s\d{4,5}-\d{4}$' OR telefone ~ '^\d{10,11}$');
```

Estas validações garantem que os dados inseridos atendam a padrões mínimos de qualidade, reduzindo a necessidade de validações complexas na camada de aplicação e garantindo consistência mesmo se os dados forem inseridos diretamente no banco de dados.

## Configuração no Amazon RDS

### Parâmetros de Configuração

Para otimizar a performance e segurança da instância PostgreSQL no Amazon RDS, algumas configurações específicas são recomendadas:

```sql
-- Configurações de performance
SET shared_preload_libraries = 'pg_stat_statements';
SET track_activity_query_size = 2048;
SET log_statement = 'mod';
SET log_min_duration_statement = 1000;

-- Configurações de segurança
SET ssl = on;
SET password_encryption = 'scram-sha-256';
```

### Backup e Recuperação

O Amazon RDS fornece funcionalidades automáticas de backup que devem ser configuradas adequadamente:

- Período de retenção de backup: 7 dias (mínimo recomendado para produção)
- Janela de backup: Durante horários de menor uso da aplicação
- Backup automático habilitado
- Snapshots manuais antes de atualizações importantes

### Monitoramento

Métricas importantes para monitorar a saúde do banco de dados:

- CPU Utilization
- Database Connections
- Free Storage Space
- Read/Write IOPS
- Database Load (Average Active Sessions)

## Scripts de Manutenção

### Script de Limpeza de Dados

```sql
-- Limpeza de registros de teste (usar com cuidado em produção)
DELETE FROM "Tb01-Cliente" 
WHERE email LIKE '%@test.com' 
   OR email LIKE '%@exemplo.com';

-- Atualização de timestamps para registros sem data_atualizacao
UPDATE "Tb01-Cliente" 
SET data_atualizacao = CURRENT_TIMESTAMP 
WHERE data_atualizacao IS NULL;
```

### Script de Análise de Performance

```sql
-- Análise de uso de índices
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes 
WHERE tablename = 'Tb01-Cliente';

-- Análise de estatísticas da tabela
SELECT 
    n_tup_ins as inserções,
    n_tup_upd as atualizações,
    n_tup_del as exclusões,
    n_live_tup as registros_ativos,
    n_dead_tup as registros_mortos
FROM pg_stat_user_tables 
WHERE relname = 'Tb01-Cliente';
```

## Migração e Versionamento

### Estratégia de Migração

Para facilitar atualizações futuras do esquema de banco de dados, é recomendável implementar um sistema de versionamento de migrations:

```sql
-- Tabela de controle de migrations
CREATE TABLE schema_migrations (
    version VARCHAR(255) PRIMARY KEY,
    applied_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Registro da migration inicial
INSERT INTO schema_migrations (version) VALUES ('001_create_cliente_table');
```

### Migrations Futuras

Exemplos de migrations que podem ser necessárias no futuro:

```sql
-- Migration 002: Adicionar campo de status do cliente
ALTER TABLE "Tb01-Cliente" 
ADD COLUMN status VARCHAR(20) DEFAULT 'ativo' 
CHECK (status IN ('ativo', 'inativo', 'suspenso'));

-- Migration 003: Adicionar campo de endereço
ALTER TABLE "Tb01-Cliente" 
ADD COLUMN endereco TEXT;

-- Migration 004: Adicionar campo de data de nascimento
ALTER TABLE "Tb01-Cliente" 
ADD COLUMN data_nascimento DATE;
```

## Considerações de Segurança

### Criptografia

Todos os dados sensíveis devem ser tratados com cuidado especial. Embora os dados de cliente nesta aplicação não sejam classificados como altamente sensíveis, algumas práticas de segurança são recomendadas:

- Utilização de conexões SSL/TLS para todas as comunicações com o banco de dados
- Criptografia em repouso habilitada no Amazon RDS
- Acesso ao banco de dados restrito através de Security Groups
- Credenciais de banco de dados gerenciadas através do AWS Secrets Manager

### Controle de Acesso

```sql
-- Criação de usuário específico para a aplicação
CREATE USER bradservless_app WITH PASSWORD 'senha_segura_gerada';

-- Concessão de permissões mínimas necessárias
GRANT CONNECT ON DATABASE "DB_Bradesco" TO bradservless_app;
GRANT USAGE ON SCHEMA public TO bradservless_app;
GRANT SELECT, INSERT, UPDATE, DELETE ON "Tb01-Cliente" TO bradservless_app;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA public TO bradservless_app;
```

## Conclusão

A modelagem do banco de dados PostgreSQL para a aplicação BradServless foi projetada com foco na simplicidade, performance e escalabilidade. A estrutura da tabela `Tb01-Cliente` atende completamente aos requisitos funcionais especificados, enquanto incorpora boas práticas de design de banco de dados que facilitarão a manutenção e evolução futura do sistema.

A utilização de UUIDs como chaves primárias, juntamente com as restrições de integridade adequadas, garante que o sistema seja robusto e adequado para uma arquitetura serverless distribuída. As configurações recomendadas para o Amazon RDS asseguram que a performance e segurança sejam mantidas em níveis adequados para um ambiente de produção.

Os scripts de criação, manutenção e migração fornecidos neste documento servem como base sólida para a implementação e evolução contínua do banco de dados, garantindo que a aplicação possa crescer e se adaptar às necessidades futuras do negócio.

