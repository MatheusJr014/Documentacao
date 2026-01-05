
# 📘 Documentação de Manutenção de Aplicativo em Produção (fora de monorepo)

Este guia descreve o passo a passo necessário para configurar o ambiente local, rodar os serviços e realizar manutenção em um aplicativo que já está em produção, mas **não está dentro de um monorepo**.

---

## 📑 Índice
- [🚨 Solução de Problemas Comuns](#-solução-de-problemas-comuns)
- [🔧 Passo 1 – Clonar os Repositórios](#-passo-1--clonar-os-repositórios)
- [🔄 Passo 2 – Atualizar os Repositórios](#-passo-2--atualizar-os-repositórios)
- [📦 Passo 3 – Instalar Dependências](#-passo-3--instalar-dependências)
- [📦 Passo 4 – Configurar e Rodar Docker](#-passo-4--configurar-e-rodar-docker)
- [💻 Passo 5 – Abrir no VS Code](#-passo-5--abrir-no-vs-code)
- [🖥️ Passo 6 – Preparar Terminais](#️-passo-6--preparar-terminais)
- [🚀 Passo 8 – Rodar os Projetos](#-passo-8--rodar-os-projetos)
- [🌐 Passo 9 – Configurar Aplicativo na Nuvem Shop](#-passo-9--configurar-aplicativo-na-nuvem-shop)
- [🎨 Passo 10 – Rodando o FrontStore](#-passo-10--rodando-o-frontstore)
- [🔗 Passo 11 – Como pegar a URL do FrontStore?](#-passo-11--como-pegar-a-url-do-frontstore)
- [📝 Passo 12 – Finalizando](#-passo-12--finalizando)

---

## 🚨 Solução de Problemas Comuns

### ❌ Erro: "Not Found Store Data"

Este erro ocorre quando o banco de dados MongoDB não possui os dados necessários da store API. Siga os passos abaixo para resolver:

#### 🔍 Passo 1: Acessar o MongoDB
1. Abra o **3T MongoDB** (MongoDB Compass ou Studio 3T)
2. Conecte-se ao banco de dados do seu projeto

#### 📊 Passo 2: Verificar Dados da Store API
1. Navegue até a coleção **`store_apis`**
2. Execute o comando de consulta:
```javascript
db.getCollection("store_apis").find({})
```

#### ⚠️ Passo 3: Identificar o Problema
- **Se retornar 0 linhas**: A coleção está vazia e precisa ser populada
- **Se retornar dados**: Verifique se os dados estão corretos para sua aplicação

#### ➕ Passo 4: Adicionar Dados (se necessário)
1. Clique em **"Add Document"**
2. Cole o seguinte JSON de exemplo - (JSON que provavelmente já existe, necessário se informar sobre) (substitua os valores pelos dados reais da sua store):

```json
{
    "_id": "exemplo_id",
    "user_id": "exemplo_user",
    "access_token": "exemplo_token",
    "admin_language": "exemplo_language",
    "app_status": "exemplo_status",
    "createdAt": "exemplo",
    "email": "exemplo_email",
    "main_currency": "exemplo_currency",
    "main_language": "exemplo_language",
    "name": "exemplo_name",
    "original_domain": "exemplo_domain",
    "phone": "exemplo_phone",
    "plan_name": "exemplo",
    "scope": "exemplo",
    "status": "exemplo",
    "stripe_customer_id": "exemplo",
    "stripe_subscription_id": "exemplo",
    "token_type": "exemplo",
    "updatedAt": "exemplo"
}
```

#### ✅ Passo 5: Confirmar e Testar
1. Confirme a ação no MongoDB
2. Execute novamente sua aplicação
3. A próxima requisição deve retornar **Status 200 OK**

---

## 🔧 Passo 1 – Clonar os Repositórios
1. Crie uma pasta base chamada **`Huapps`**.  
2. Dentro dela, siga a estrutura abaixo:

```
Huapps
 ├─ apps
 │   ├─ <Nome-do-app> (Ex: ns-compre-junto-pro)
 │   │   ├─ ns-compre-junto-pro-react     # Frontend
 │   │   └─ ns-compre-junto-pro-service   # Backend
 │   └─ ns-commons-new                    # Commons
 │
 ├─ frontstore
 │
 └─ middleware-ns-service
```

---

## 🔄 Passo 2 – Atualizar os Repositórios
- Entre em **cada pasta clonada** e rode:
```bash
git pull
```

---

## 📦 Passo 3 – Instalar Dependências
- Ainda em cada pasta clonada, rode:
```bash
npm install
```


## 📦 Passo 4 – Configurar e Rodar Docker

Para rodar os aplicativos em modo de manutenção, é necessário configurar e executar o **Docker do monorepo**. O Docker do monorepo é responsável por gerenciar os serviços necessários para que os aplicativos funcionem corretamente no ambiente local.

### 🔧 Configuração do Docker

Antes de iniciar o Docker, é necessário realizar algumas configurações essenciais:

#### 📥 Instalar Dependências do Monorepo

Caso ainda não tenha instalado as dependências do monorepo, é necessário fazer isso antes de prosseguir:

- Navegue até a **pasta raiz do projeto monorepo**
- Execute o comando para instalar todas as dependências:

```bash
pnpm install
```

> ⚠️ **Importante:** Certifique-se de estar na pasta raiz do monorepo ao executar este comando.

#### 1️⃣ Carregar o StoreApps no Banco de Dados

O primeiro passo é garantir que os dados do **StoreApps** estejam carregados no banco de dados. Isso é fundamental para que a aplicação tenha acesso às informações necessárias das lojas.

- Acesse o banco de dados (3TMongoDB)
- Certifique-se de que a coleção **`storeapps`** está populada com os dados necessários
- Caso não estiver, popule o banco com os storeapps. **Exemplo:**

```javascript
{
    "_id": ObjectId("68f7e3446cb3ba8dea12d5b5"),
    "store_id": NumberInt(########),
    "app_slug": "ns-compra-rapida",
    "access_token": "######################",
    "installed_at": ISODate("2025-10-21T19:47:16.726+0000"),
    "uninstalled_at": null,
    "is_installed": true,
    "billing_status": "trial",
    "trial_started_at": ISODate("2025-10-21T19:47:16.726+0000"),
    "trial_ends_at": ISODate("2026-10-31T19:47:16.726+0000"),
    "uninstall_cleanup_scheduled_for": null,
    "is_paused": false,
    "is_demo": false,
    "created_at": ISODate("2025-10-21T19:47:16.734+0000"),
    "updated_at": ISODate("2025-10-28T00:07:44.837+0000"),
    "__v": NumberInt(0),
    "onboard_notifications": {
        "app_instalado_pt": ISODate("2025-10-22T11:11:58.481+0000"),
        "trial_end_reminder_pt": ISODate("2025-10-28T00:07:44.831+0000")
    }
}
```


#### 2️⃣ Cadastrar os Apps na API da HUAPPS

Para que os aplicativos sejam reconhecidos e possam rodar em modo de manutenção, é necessário cadastrá-los na **API da HUAPPS**. Este cadastro permite que o sistema identifique quais aplicativos estão disponíveis e como devem ser executados.

- Lembre de **SEMPRE** estar em modo DEV no **Postman** 
- Acesse a API da HUAPPS (normalmente rodando localmente ou em ambiente de desenvolvimento) através do **Postman**
- Cadastre o aplicativo que você está mantendo
- Configure as informações necessárias (nome, slug, URLs, etc.)

**Exemplo:**

```javascript
{
    "_id": ObjectId("68f7e26a6cb3ba8dea12d5a6"),
    "app_slug": "ns-compra-rapida",
    "app_id": NumberInt(19731),
    "client_secret": "##########################",
    "name": {
        "pt": "Compra Rapida Pro",
        "en": "Compra Rapida Pro",
        "es": "Compra Rapida Pro"
    },
    "short_description": {
        "pt": "Compra Rapida Pro",
        "en": "Compra Rapida Pro",
        "es": "Compra Rapida Pro"
    },
    "description": {
        "pt": "App completo para montar e gerenciar listas de presentes personalizadas.",
        "en": "A complete app to build and manage personalized gift lists.",
        "es": "Una app completa para crear y administrar listas de regalos personalizadas."
    },
    "banners": [
        "https://cdn.huapps.com.br/banners/lista1.png",
        "https://cdn.huapps.com.br/banners/lista2.png"
    ],
    "thumbnail_url": "https://cdn.huapps.com.br/thumbnails/lista-icon.png",
    "support": {
        "email": "suporte@huapps.com.br",
        "help_url": "https://ajuda.huapps.com.br/compra-rapida",
        "terms_url": "https://huapps.com.br/termos",
        "config_url": "https://lista.huapps.com.br/config"
    },
    "urls": {
        "panel_url": "http://127.0.0.1:3999",
        "api_url": "http://ns-compra-rapida:3122",
        "marketplace_url": "https://www.nuvemshop.com.br/apps/compra-rapida"
    },
    "trial_days": NumberInt(7),
    "stripe_product_id": "prod_SMezV4CAEeT4Ai",
    "enhanced": true,
    "published": true,
    "show_in_marketplace": true,
    "videos": {
        "presentation": "https://youtu.be/presentacao123",
        "tutorials": {
            "pt": "https://youtube.com/playlist?list=br",
            "es": "https://youtube.com/playlist?list=es"
        }
    },
    "faq": [
        {
            "question": "Como configuro o app?",
            "answer": "Acesse o painel e siga o passo a passo.",
            "_id": ObjectId("68f7e26a6cb3ba8dea12d5a7")
        },
        {
            "question": "Existe período de teste?",
            "answer": "Sim, o app oferece 7 dias gratuitos.",
            "_id": ObjectId("68f7e26a6cb3ba8dea12d5a8")
        }
    ],
    "slack_channel": "#################",
    "created_at": ISODate("2025-10-21T19:43:38.619+0000"),
    "updated_at": ISODate("2025-10-21T19:43:38.619+0000"),
    "__v": NumberInt(0)
}
```


#### 3️⃣ Rodar o Docker (Monorepo) através do Make Dev

Com as configurações anteriores concluídas, você pode iniciar o Docker do monorepo:

```bash
make dev
```

Este comando irá:
- Inicializar todos os containers Docker necessários
- Configurar os serviços de banco de dados
- Preparar o ambiente para execução dos aplicativos
- Disponibilizar as APIs e serviços auxiliares

> ⚠️ **Importante:** Certifique-se de estar na raiz do monorepo ao executar o comando `make dev`.

---

## 💻 Passo 5 – Abrir no VS Code
- Abra a pasta `apps` diretamente no **Visual Studio Code**.

---

## 🖥️ Passo 6 – Preparar Terminais
- Dentro do VS Code, abra **4 terminais** (aba `Terminal` → `Novo Terminal`).

---


## 🚀 Passo 8 – Rodar os Projetos
- Abra cada pasta em um terminal separado e rode os comandos de inicialização:
  - `monorepo` 
  - `frontstore`
  - `ns-compre-junto-pro-react`
  - `ns-compre-junto-pro-service`

> ℹ️ Os comandos de start variam conforme a configuração do projeto, normalmente:
```bash
npm run dev
```
ou
```bash
npm start
```

---

## 🌐 Passo 9 – Configurar Aplicativo na Nuvem Shop
1. Acesse o **Painel da Nuvem Shop**.  
2. Vá em **Aplicativo de Teste**.  
3. Configure a **URL local** para apontar para o ambiente de desenvolvimento.

---

## 🎨 Passo 10 – Rodando o FrontStore
1. Acesse:
   - **Parceiros Nuvem Shop** → **Aplicativos**.  
   - Selecione o aplicativo que deseja modificar.  
   - Vá em **Scripts**.  
   - Clique no **lápis (editar)** na última versão.  
   - Coloque em **modo Desenvolvimento**.  
   - Cole a URL do **FrontStore**.

---

## 🔗 Passo 11 – Como pegar a URL do FrontStore?
1. Rode o projeto localmente.  
2. Na raiz, localize o caminho dentro da pasta **dist**:

```
/frontstore-apps/nuvemshop/<NOME_DO_APP>/<app.debug.js>
```

3. Adicione o prefixo:
```
http://localhost:9991/
```

👉 Exemplo completo:
```
http://localhost:9991/frontstore-apps/nuvemshop/desconto-por-qtd/app.debug.js
```

⚠️ **Atenção:**  
- Em **modo DEV** sempre utilizar **`.debug.js`**.  
- Em **produção**, o sufixo será **`.min.js`**.  

---

## 📝 Passo 12 – Finalizando
- Cole a **URL do FrontStore** no campo de **Modo Desenvolvimento** dentro da Nuvem Shop.  
- Salve as alterações.  
- O app já estará rodando com a versão local.  

---

✅ **Pronto!** Agora você tem todo o ambiente configurado para manutenção do app.
