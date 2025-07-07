# Servus - Sistema de Gestão de Voluntários para Igrejas

Um sistema completo para gerenciar voluntários, escalas e ministérios em igrejas.

## 🚀 Funcionalidades

- **Autenticação**: Login e registro com Supabase
- **Gestão de Voluntários**: Cadastro e gerenciamento de voluntários
- **Escalas Automáticas**: Sistema inteligente de criação de escalas
- **Ministérios**: Organização por ministérios e habilidades
- **Portal do Voluntário**: Interface para voluntários gerenciarem sua disponibilidade
- **Portal do Coordenador**: Ferramentas avançadas para coordenadores
- **Notificações**: Sistema de notificações em tempo real
- **Relatórios**: Geração de relatórios e estatísticas

## 🛠️ Tecnologias

- **Frontend**: React + TypeScript + Vite
- **Styling**: Tailwind CSS
- **Icons**: Lucide React
- **Backend**: Supabase
- **Autenticação**: Supabase Auth
- **Banco de Dados**: PostgreSQL (Supabase)

## 📋 Pré-requisitos

- Node.js 18+
- Conta no Supabase

## ⚙️ Configuração

### 1. Clone o repositório

```bash
git clone <repository-url>
cd servus
```

### 2. Instale as dependências

```bash
npm install
```

### 3. Configure o Supabase

1. Crie um projeto no [Supabase](https://supabase.com)
2. Copie o arquivo `.env.example` para `.env`
3. Adicione suas credenciais do Supabase:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### 4. Configure o banco de dados

Execute os seguintes comandos SQL no editor SQL do Supabase:

```sql
-- Criar tabela de perfis
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) PRIMARY KEY,
  name TEXT NOT NULL,
  email TEXT NOT NULL,
  phone TEXT,
  address TEXT,
  role TEXT NOT NULL CHECK (role IN ('coordenador', 'voluntario')),
  church TEXT NOT NULL DEFAULT 'Igreja Comunidade da Graça',
  ministry TEXT[] DEFAULT '{}',
  has_coordinator_access BOOLEAN DEFAULT FALSE,
  coordinator_request_status TEXT CHECK (coordinator_request_status IN ('pendente', 'aprovado', 'rejeitado')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Habilitar RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Políticas de segurança
CREATE POLICY "Users can read own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);

CREATE POLICY "Users can insert own profile" ON profiles
  FOR INSERT WITH CHECK (auth.uid() = id);

-- Função para atualizar updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ language 'plpgsql';

-- Trigger para atualizar updated_at
CREATE TRIGGER update_profiles_updated_at
  BEFORE UPDATE ON profiles
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at_column();
```

### 5. Execute o projeto

```bash
npm run dev
```

O projeto estará disponível em `http://localhost:5173`

## 📁 Estrutura do Projeto

```
src/
├── components/          # Componentes reutilizáveis
├── contexts/           # Contextos React (Auth, etc.)
├── hooks/              # Hooks customizados
├── integrations/       # Integrações (Supabase)
├── pages/              # Páginas da aplicação
├── services/           # Serviços e APIs
└── types/              # Tipos TypeScript
```

## 🔐 Autenticação

O sistema usa Supabase Auth para autenticação. Os usuários podem:

- Fazer login com email e senha
- Registrar nova conta
- Recuperar senha (implementação futura)
- Logout automático

## 👥 Tipos de Usuário

### Voluntário
- Visualizar sua escala
- Gerenciar disponibilidade
- Solicitar trocas de turno
- Atualizar perfil
- Solicitar acesso de coordenador

### Coordenador
- Todas as funcionalidades de voluntário
- Gerenciar outros voluntários
- Criar e editar escalas
- Aprovar solicitações de acesso
- Visualizar relatórios

## 🚀 Deploy

O projeto pode ser facilmente deployado no Netlify:

```bash
npm run build
```

Configure as variáveis de ambiente no painel do Netlify com as mesmas variáveis do arquivo `.env`.

## 📝 Licença

Este projeto está sob a licença MIT.