# 🎒 ESPE — Sistema de Gestão de Aulas
## Guia completo de instalação (Supabase + GitHub + Streamlit Cloud)

Este guia leva você do zero ao sistema no ar. Não precisa saber programar — é só
seguir os passos na ordem. Reserve cerca de **1 hora** na primeira vez.

O que você vai ter no final: um site com endereço próprio (ex:
`espe-aulas.streamlit.app`) onde a gestora entra com senha e cada professora entra
com PIN, com seus dados reais já carregados.

---

## Visão geral (as 3 peças)

| Peça | Para quê | Custo |
|------|----------|-------|
| **Supabase** | Guarda os dados (alunos, aulas, pagamentos) | Grátis |
| **GitHub** | Guarda o código do sistema | Grátis |
| **Streamlit Cloud** | Coloca o site no ar | Grátis |

Você vai criar uma conta em cada um (pode usar o mesmo e-mail).

---

## PARTE 1 — Banco de dados (Supabase)

### 1.1 Criar a conta e o projeto
1. Acesse **https://supabase.com** e clique em **Start your project**.
2. Entre com sua conta Google ou GitHub.
3. Clique em **New project**.
   - **Name:** `espe-aulas`
   - **Database Password:** crie uma senha forte e **anote num lugar seguro**.
   - **Region:** escolha `South America (São Paulo)`.
4. Clique em **Create new project** e aguarde uns 2 minutos.

### 1.2 Criar as tabelas
1. No menu lateral do Supabase, clique em **SQL Editor**.
2. Clique em **+ New query**.
3. Abra o arquivo **`schema.sql`** (que veio neste pacote), copie **todo** o conteúdo
   e cole na caixa de texto.
4. Clique em **Run** (ou aperte `Ctrl+Enter`).
5. Deve aparecer **Success. No rows returned** — está tudo certo, as tabelas foram criadas.

### 1.3 Pegar as chaves de acesso
1. No menu lateral, clique na engrenagem **Project Settings** > **API**.
2. Anote dois valores (vamos usar daqui a pouco):
   - **Project URL** — algo como `https://abcdefgh.supabase.co`
   - **service_role** key (em *Project API keys*) — uma chave longa.
     ⚠️ Essa é secreta, nunca compartilhe nem coloque em lugar público.

---

## PARTE 2 — Importar os dados da planilha

Isso roda **uma única vez**, no seu computador, para subir alunos, professoras e as
92 aulas da planilha atual para o Supabase.

### 2.1 Instalar o Python (se ainda não tiver)
- Baixe em **https://www.python.org/downloads/** e instale.
- No Windows, marque a caixa **"Add Python to PATH"** durante a instalação.

### 2.2 Rodar a importação
1. Abra o **Terminal** (Mac) ou **Prompt de Comando / PowerShell** (Windows).
2. Vá até a pasta do projeto. Exemplo:
   ```
   cd Downloads/espe_sistema
   ```
3. Instale a biblioteca necessária:
   ```
   pip install supabase
   ```
4. Informe suas chaves do Supabase (use os valores da etapa 1.3):

   **Mac/Linux:**
   ```
   export SUPABASE_URL="https://abcdefgh.supabase.co"
   export SUPABASE_KEY="sua_service_role_key"
   ```
   **Windows (PowerShell):**
   ```
   $env:SUPABASE_URL="https://abcdefgh.supabase.co"
   $env:SUPABASE_KEY="sua_service_role_key"
   ```
5. Rode o importador:
   ```
   python scripts/importar_dados.py
   ```
6. Deve aparecer `✅ Importação concluída!` com a contagem de professoras, alunos e aulas.

> **PIN inicial de todas as professoras: `1234`.** Você troca depois dentro do
> sistema (ou direto no Supabase).

### 2.3 Conferir (opcional)
No Supabase, vá em **Table Editor** e clique na tabela `aulas` — você verá as 92
aulas importadas.

---

## PARTE 3 — Subir o código (GitHub)

### 3.1 Criar a conta e o repositório
1. Acesse **https://github.com** e crie uma conta (se não tiver).
2. Clique no **+** no canto superior direito > **New repository**.
   - **Repository name:** `espe-aulas`
   - Marque **Private** (recomendado, para o código não ficar público).
   - **Não** marque "Add a README".
3. Clique em **Create repository**.

### 3.2 Enviar os arquivos
A forma mais simples, sem instalar nada:
1. Na página do repositório recém-criado, clique em **uploading an existing file**.
2. Arraste para a janela **todos** os arquivos do projeto **MENOS**:
   - a pasta `scripts/` (não precisa subir — já rodou a importação)
   - o arquivo `secrets.toml` (se você criou um preenchido)

   Os arquivos a subir são: `app.py`, `db.py`, `pdf_gen.py`, `branding.py`,
   `requirements.txt`, `.gitignore`, `schema.sql`, e a pasta `.streamlit/`
   (com `config.toml` e o `secrets.toml.exemplo`).

   > Dica: se a pasta `.streamlit` não aparecer ao arrastar, crie o arquivo
   > manualmente no GitHub com o botão **Add file > Create new file** e nomeie
   > `.streamlit/config.toml`, colando o conteúdo.
3. Clique em **Commit changes**.

---

## PARTE 4 — Colocar no ar (Streamlit Cloud)

### 4.1 Conectar
1. Acesse **https://share.streamlit.io** e clique em **Sign in with GitHub**.
2. Autorize o acesso quando pedir.

### 4.2 Criar o app
1. Clique em **Create app** > **Deploy a public app from GitHub**.
2. Preencha:
   - **Repository:** `seu-usuario/espe-aulas`
   - **Branch:** `main`
   - **Main file path:** `app.py`
3. **Antes de clicar em Deploy**, clique em **Advanced settings** > campo **Secrets**.
4. Cole o conteúdo abaixo, trocando pelos seus valores reais:
   ```toml
   SUPABASE_URL = "https://abcdefgh.supabase.co"
   SUPABASE_KEY = "sua_service_role_key"
   SENHA_GESTORA = "espe2026"
   ```
   (Troque `espe2026` pela senha que a gestora vai usar.)
5. Clique em **Deploy**.
6. Aguarde 2–3 minutos. Quando terminar, o sistema estará no ar com um endereço
   tipo `https://espe-aulas.streamlit.app`.

---

## PARTE 5 — Primeiro acesso

- **Gestora:** escolha "Gestora" e digite a senha que você pôs em `SENHA_GESTORA`.
- **Professora:** escolha "Professora", selecione o nome e use o PIN `1234`.

Pronto! O sistema está funcionando com os dados reais da planilha.

---

## Dúvidas comuns

**"Esqueci de trocar os PINs das professoras."**
Cada professora começa com `1234`. Para trocar, abra o Supabase > Table Editor >
`professoras`, clique na linha da professora e edite a coluna `pin`.

**"Quero mudar o mês que aparece (hoje está Abril)."**
No arquivo `app.py`, no topo, mude as linhas `MES_LABEL = "Abril"` e
`MES_ANO = "Abril 2026"`. Salve no GitHub e o Streamlit atualiza sozinho.

**"O site mostra erro de conexão."**
Quase sempre é segredo errado. No Streamlit Cloud, vá em **Settings > Secrets** e
confira se a URL e a chave do Supabase estão corretas.

**"Como adiciono uma aula nova no dia a dia?"**
Entre como gestora > aba **Aulas** > **Registrar nova aula**. O sistema já sugere o
valor com base no pacote do aluno.

**"Os dados somem quando fecho o navegador?"**
Não. Tudo fica salvo no Supabase. Você pode fechar e abrir quando quiser.

---

## O que cada arquivo faz (referência)

| Arquivo | Função |
|---------|--------|
| `app.py` | O sistema em si (telas da gestora e da professora) |
| `db.py` | Conversa com o banco Supabase |
| `pdf_gen.py` | Gera os PDFs (recibo, extrato, fechamento) |
| `branding.py` | Cores e identidade visual da ESPE |
| `schema.sql` | Cria as tabelas no Supabase (roda uma vez) |
| `scripts/importar_dados.py` | Importa a planilha para o banco (roda uma vez) |
| `requirements.txt` | Lista de bibliotecas que o sistema usa |
| `.streamlit/config.toml` | Tema visual do Streamlit |

---

*ESPE • educação que acolhe, conecta e transforma*
