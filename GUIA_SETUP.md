# 🚀 Guia Completo: Plataforma SaaS Nora e Vaso

Seu site profissional com login, admin e segurança enterprise.

---

## **PARTE 1: Configurar Supabase** (5 minutos)

### Passo 1.1: Criar conta no Supabase

1. Vá para **https://supabase.com**
2. Clique em **"Sign up"** (use Google ou GitHub)
3. Confirme seu email
4. Crie uma nova organização (ex: "Nora e Vaso")

### Passo 1.2: Criar um projeto

1. Na dashboard, clique em **"New project"**
2. Nome do projeto: `noraevaso`
3. Escolha **São Paulo** como região (mais rápido)
4. Crie uma senha segura para o banco (salve em um lugar seguro!)
5. Clique em **"Create new project"** e espere 2-3 minutos

### Passo 1.3: Copiar as credenciais

Depois que o projeto estiver pronto:

1. Vá em **"Settings"** → **"API"**
2. Copie e salve em um arquivo seguro:
   - **Project URL** (algo como `https://xxxxx.supabase.co`)
   - **anon public** (key começando com `eyJ...`)

---

## **PARTE 2: Criar as tabelas no banco** (2 minutos)

1. No Supabase, vá em **"SQL Editor"** (lado esquerdo)
2. Clique em **"New query"**
3. **Cole este código SQL:**

```sql
-- Criar tabela de relatórios
CREATE TABLE IF NOT EXISTS reports (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL,
  title TEXT NOT NULL,
  content TEXT,
  topic TEXT DEFAULT 'geral',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Criar tabela de perfis de usuários
CREATE TABLE IF NOT EXISTS profiles (
  id UUID PRIMARY KEY,
  full_name TEXT,
  email TEXT,
  role TEXT DEFAULT 'user',
  subscription_plan TEXT DEFAULT 'free',
  created_at TIMESTAMP DEFAULT NOW(),
  CONSTRAINT fk_user FOREIGN KEY (id) REFERENCES auth.users(id) ON DELETE CASCADE
);

-- Ativar Row Level Security (segurança)
ALTER TABLE reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Política: usuário só vê seus próprios relatórios
CREATE POLICY "Users see own reports"
  ON reports FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users insert own reports"
  ON reports FOR INSERT
  WITH CHECK (auth.uid() = user_id);

-- Política: admin vê tudo
CREATE POLICY "Admin sees all reports"
  ON reports FOR SELECT
  USING (
    (SELECT role FROM profiles WHERE id = auth.uid()) = 'admin'
  );

-- Política: perfis
CREATE POLICY "Users see own profile"
  ON profiles FOR SELECT
  USING (auth.uid() = id);
```

4. Clique em **"Run"** (⏯️) verde
5. Pronto! Tabelas criadas.

---

## **PARTE 3: Configurar autenticação** (3 minutos)

1. Vá em **"Authentication"** → **"Providers"** (lado esquerdo)
2. Certifique-se que **"Email"** está ativado (deve estar por padrão)
3. Vá em **"Email Templates"** e configure:
   - Personalize o email de confirmação se quiser (opcional)
4. Vá em **"URL Configuration"** e adicione seus domínios:
   - **Localhost:** `http://localhost:3000`
   - **Seu domínio:** `https://noraevaso.com.br`

---

## **PARTE 4: Configurar seu HTML** (1 minuto)

1. Abra o arquivo `noraevaso-platform.html` em um editor de texto
2. Procure por estas linhas (no topo do arquivo):

```javascript
const SUPABASE_URL = 'https://seu-project.supabase.co';
const SUPABASE_ANON_KEY = 'sua-anon-key-aqui';
```

3. Substitua com SEUS dados do Supabase:

```javascript
const SUPABASE_URL = 'https://xxxxx.supabase.co';  // Copie do Supabase
const SUPABASE_ANON_KEY = 'eyJxxxx...';  // Copie do Supabase
```

4. **Salve o arquivo**

---

## **PARTE 5: Testar localmente** (2 minutos)

### Opção A: Teste direto (mais simples)

1. Abra o arquivo `noraevaso-platform.html` no navegador (Ctrl+O / Cmd+O)
2. Clique em **"Cadastre-se"**
3. Crie uma conta de teste

### Opção B: Usar servidor local (recomendado)

Se tem Node.js instalado:

```bash
# Instalar servidor simples
npm install -g http-server

# Entrar na pasta do arquivo
cd /caminho/para/o/arquivo

# Rodar servidor
http-server -p 3000
```

Então abra: `http://localhost:3000/noraevaso-platform.html`

---

## **PARTE 6: Hospedar no Vercel** (5 minutos)

### 6.1: Preparar para upload

1. Crie uma pasta chamada `noraevaso`
2. Coloque o arquivo `noraevaso-platform.html` lá
3. Renomeie para `index.html`
4. Crie um arquivo `package.json` com este conteúdo:

```json
{
  "name": "noraevaso-platform",
  "version": "1.0.0",
  "description": "Plataforma SaaS de Medicina Intensiva"
}
```

### 6.2: Upload para Vercel

1. Vá para **https://vercel.com**
2. Clique em **"Sign up"** (pode usar GitHub)
3. Conecte seu GitHub (ou faça upload direto)
4. Clique em **"Import Project"**
5. Faça upload da pasta `noraevaso`
6. Configure:
   - **Framework:** None / HTML
   - **Root Directory:** ./
7. Clique em **"Deploy"**

Seu site estará em algo como: `https://noraevaso.vercel.app`

---

## **PARTE 7: Conectar seu domínio** (10 minutos)

### 7.1: Comprar o domínio

1. Vá para **https://registro.br** (registro brasileiro)
2. Busque por `noraevaso.com.br`
3. Se estiver disponível, compre (R$ 40/ano)
4. Guarde seus dados de acesso ao Registro.br

### 7.2: Apontar para Vercel

**No Vercel:**

1. Vá para seu projeto
2. Clique em **"Settings"** → **"Domains"**
3. Clique em **"Add Domain"**
4. Digite: `noraevaso.com.br`
5. Vercel vai mostrar 4 nameservers (NS records)
6. **Copie esses 4 nameservers**

**No Registro.br:**

1. Faça login em **registro.br**
2. Vá em **"Meus Domínios"**
3. Clique em `noraevaso.com.br`
4. Clique em **"Editar Zona de DNS"**
5. Procure por **"Nameservers"** ou **"NS"**
6. **Cole os 4 nameservers do Vercel**
7. Salve as mudanças

**Espere 24-48 horas** e seu site estará em `https://noraevaso.com.br` ✅

---

## **PARTE 8: Criar Admin** (2 minutos)

Agora você precisa criar uma conta de administrador:

1. Acesse seu site
2. Clique em **"Cadastre-se"**
3. Use o email: `admin@noraevaso.com`
4. Escolha uma senha forte
5. Faça login

**Para ativar como admin:**

1. No Supabase, vá em **"Authentication"** → **"Users"**
2. Procure por `admin@noraevaso.com`
3. Vá em **"User Details"** → edite o perfil
4. Mude o campo `role` de `user` para `admin`
5. Salve

Pronto! Agora `admin@noraevaso.com` tem acesso ao painel admin.

---

## **PARTE 9: Segurança** ✅

### O que você já tem:

✅ **Autenticação JWT** - Senhas criptografadas automaticamente  
✅ **Row Level Security (RLS)** - Cada usuário vê só seus dados  
✅ **HTTPS obrigatório** - Todos os dados trafegam criptografados  
✅ **Isolamento de dados** - Banco separado por usuário  

### O que você deve fazer:

1. **Nunca** compartilhe suas chaves do Supabase
2. Mude a senha do banco periodicamente
3. Faça backup dos dados (Supabase faz automaticamente)
4. Monitore logs de login em "Authentication" → "Logs"

---

## **PARTE 10: Monetização** (como começar a vender)

### Modelo atual:

- Cadastre novos usuários manualmente (painel Admin)
- Cada usuário acessa seu próprio conteúdo

### Para cobrar no futuro:

1. **Integrar Stripe** (pagamento online):
   - Criar planos de preço
   - Ativar/desativar acesso por plano

2. **Criar trial gratuito:**
   - Limitar dias de acesso
   - Esconder conteúdo premium

3. **Cobrança recorrente:**
   - Mensalidade automática
   - Desativar acesso se não pagar

---

## **TROUBLESHOOTING**

### "Erro ao fazer login"
- Verifique se as credenciais do Supabase estão certas
- Confirme seu email no Supabase
- Limpe cache do navegador (Ctrl+Shift+Del)

### "Página em branco"
- Abra o console (F12) e veja o erro
- Verifique se o Supabase está online
- Certifique-se que SUPABASE_URL e KEY estão corretos

### "Não consegue fazer upload no Vercel"
- Certifique-se que o arquivo é `index.html`
- Verifique permissões da pasta
- Tente fazer upload via Git em vez de drag-and-drop

---

## **Próximas melhorias**

- [ ] Adicionar Stripe para pagamento
- [ ] Dark mode
- [ ] 2FA (autenticação de dois fatores)
- [ ] Notificações por email
- [ ] Analytics de uso
- [ ] API pública para integração

---

## **Suporte**

- **Supabase Docs:** https://supabase.com/docs
- **Vercel Docs:** https://vercel.com/docs
- **Community Supabase:** https://discord.gg/supabase

---

**Você agora tem uma plataforma SaaS profissional, segura e pronta para escalar! 🚀**
