# 📘 Guia de Criação de Novos Aplicativos (Monorepo)

Este guia descreve o processo completo para a criação de um novo aplicativo Nuvemshop, desde a configuração técnica do ambiente até o cadastro e instalação na loja de desenvolvimento.

---

## 📑 Índice
- [🔧 Parte 1: Configuração Técnica do Ambiente](#-parte-1-configuração-técnica-do-ambiente)
- [⚙️ Parte 2: Configuração e Regra de Negócio](#️-parte-2-configuração-e-regra-de-negócio)
- [📋 Passo 1: Clonar o Repositório Principal](#-passo-1-clonar-o-repositório-principal)
- [📁 Passo 2: Duplicar o app-base](#-passo-2-duplicar-o-app-base)
- [🔧 Passo 3: Configurar Variáveis de Ambiente](#-passo-3-configurar-variáveis-de-ambiente)
- [📦 Passo 4: Instalar Dependências](#-passo-4-instalar-dependências)
- [⚙️ Passo 5: Adicionar Configurações do App](#️-passo-5-adicionar-configurações-do-app)
- [🚀 Passo 6: Iniciar o Ambiente de Desenvolvimento](#-passo-6-iniciar-o-ambiente-de-desenvolvimento)
- [🔄 Passo 7: Rodar os Commons](#-passo-7-rodar-os-commons)
- [🏪 Passo 8: Criar o Aplicativo na Nuvemshop Partners](#-passo-8-criar-o-aplicativo-na-nuvemshop-partners)
- [📝 Passo 9: Cadastrar o App na API Local](#-passo-9-cadastrar-o-app-na-api-local)
- [🔗 Passo 10: Instalar o App na Loja de Teste](#-passo-10-instalar-o-app-na-loja-de-teste)
- [✅ Passo 11: Conclusão](#-passo-11-conclusão)

---

## 🔧 Parte 1: Configuração Técnica do Ambiente

Esta etapa foca em preparar a estrutura de código do novo aplicativo dentro do nosso ambiente de monorepo.

---

## 📋 Passo 1: Clonar o Repositório Principal
- Garanta que você tenha o repositório **NuvemShop Apps** clonado em sua máquina local.

---

## 📁 Passo 2: Duplicar o app-base
1. No repositório, localize as pastas do **`app-base`**
2. Copie e cole a pasta `app-base` tanto do diretório de **API** quanto do diretório de **front-end (React)**
3. Renomeie as novas pastas para o slug do seu novo aplicativo:
   - Exemplo: `meu-novo-app-api` e `meu-novo-app-react`

---

## 🔧 Passo 3: Configurar Variáveis de Ambiente (docker-compose)
1. Abra o arquivo **`docker-compose.yml`**
2. Localize a seção referente ao novo serviço que você copiou
3. Altere as variáveis de ambiente, especialmente:
   - Nome do serviço
   - Portas
   - Para que correspondam ao slug definido no passo anterior

---

## 📦 Passo 4: Instalar Dependências
- Na raiz do monorepo, execute o comando para instalar todos os pacotes e dependências necessárias:

```bash
pnpm install
```

---

## ⚙️ Passo 5: Adicionar Configurações do App
1. Navegue até o arquivo de configuração geral dos aplicativos (geralmente `app.config.js` ou similar)
2. Adicione uma nova entrada para o seu aplicativo, informando:
   - Slug do aplicativo
   - Porta designada para ele

---

## 🚀 Passo 6: Iniciar o Ambiente de Desenvolvimento
- Execute o comando para subir todo o ambiente de desenvolvimento via Docker:

```bash
make dev
```

---

## 🔄 Passo 7: Rodar os Commons
- Para garantir que as bibliotecas compartilhadas (`react-commons` e `api-commons`) reflitam qualquer alteração:
1. Abra um terminal separado
2. Execute o comando para mantê-las rodando em modo de desenvolvimento

---

## ⚙️ Parte 2: Configuração e Regra de Negócio do Aplicativo

Esta etapa foca em registrar o aplicativo na plataforma de parceiros da Nuvemshop e conectá-lo ao ambiente de desenvolvimento local.

---

## 🏪 Passo 8: Criar o Aplicativo na Nuvemshop Partners
1. Acesse o **painel de Parceiros da Nuvemshop**
2. Clique em **"Criar Aplicativo"**
3. Preencha os dados iniciais conforme a demanda do projeto:
   - Nome
   - Descrição
   - Outros dados necessários
4. **Anote o App ID e o Client Secret** fornecidos após a criação

---

## 📝 Passo 9: Cadastrar o App na API Local
1. Utilize uma ferramenta como **Postman** ou **Insomnia**
2. Faça uma requisição **POST** para cadastrar o novo aplicativo em seu ambiente de desenvolvimento local

⚠️ **Atenção:** Certifique-se de que você está apontando para o ambiente de **desenvolvimento (DEV)**, jamais para produção.

### 📄 JSON de Exemplo para Cadastro:

```json
{
    "app_slug": "o-mesmo-slug-usado-nos-passos-anteriores",
    "app_id": "ID_FORNECIDO_PELA_NUVEMSHOP",
    "client_secret": "CLIENT_SECRET_FORNECIDO_PELA_NUVEMSHOP",
    "name": {
        "pt": "Nome do App em Português",
        "en": "App Name in English",
        "es": "Nombre de la App en Español"
    },
    "short_description": {
        "pt": "Descrição curta em Português.",
        "en": "Short description in English.",
        "es": "Descripción corta en Español."
    },
    "description": {
        "pt": "Descrição completa em Português.",
        "en": "Full description in English.",
        "es": "Descripción completa en Español."
    },
    "banners": ["URL_BANNER_1", "URL_BANNER_2"],
    "thumbnail_url": "URL_DA_IMAGEM_MINIATURA",
    "support": {
        "email": "suporte@huapps.com",
        "help_url": "URL_PAGINA_DE_AJUDA",
        "terms_url": "URL_TERMOS_DE_USO",
        "config_url": "URL_DE_CONFIGURACAO"
    },
    "urls": {
        "panel_url": "http://127.0.0.1:3999",
        "api_url": "http://{slug_do_app}:{porta_definida}",
        "marketplace_url": "https://www.nuvemshop.com.br/loja-aplicativos-nuvem/{slug-do-app}?q=huapps"
    },
    "videos": {
        "presentation": "URL_VIDEO_APRESENTACAO",
        "tutorials": {
            "pt": "URL_PLAYLIST_TUTORIAIS_PT",
            "es": "URL_PLAYLIST_TUTORIAIS_ES"
        }
    },
    "trial_days": 7,
    "stripe_product_id": "prod_...",
    "stripe_price_id": "price_...",
    "prices": {
        "brl": 49.90,
        "usd": 8.75,
        "ars": 9073.0,
        "mxn": 180.14,
        "cop": 33267,
        "clp": 7006
    },
    "enhanced": true,
    "published": true,
    "faq": [
        {
            "question": "Pergunta Frequente 1?",
            "answer": "Resposta para a pergunta 1."
        }
    ],
    "slack_channel": "URL_DO_WEBHOOK_SLACK"
}
```

---

## 🔗 Passo 10: Instalar o App na Loja de Teste
1. Após cadastrar o app na API local, utilize o link de autorização abaixo
2. Substitua `{{id do app Criado}}` pelo **App ID** obtido no passo 8
3. Acesse o link no seu navegador, logado na sua loja de desenvolvimento (ex: `demohuapps`)

```
https://demohuapps.lojavirtualnuvem.com.br/admin/v2/apps/{{id do app Criado}}/authorize
```

4. Siga o fluxo de instalação e permissões

---

## ✅ Passo 11: Conclusão
- Após a autorização, o **app-base** estará instalado e funcionando em sua loja de teste
- O aplicativo estará conectado ao seu ambiente de desenvolvimento local
- Agora você pode começar a desenvolver as funcionalidades específicas do novo aplicativo

---

✅ **Pronto!** Seu novo aplicativo está configurado e pronto para desenvolvimento.