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
- [🔁 Tópico Extra: Callbacks e Webhooks (e uso de ngrok)](#-tópico-extra-callbacks-e-webhooks-e-uso-de-ngrok)
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

## 🔁 Tópico Extra: Callbacks e Webhooks (e uso de ngrok)

Este tópico existe porque, na prática, quase todo app precisa **receber eventos** (ex: Promoções, descontos ou fretes).

### ✅ O que é Callback?
- **Callback (URL de retorno)** é uma URL do seu app para onde o usuário (navegador) é redirecionado após uma ação em outra plataforma.
- **Exemplos comuns**:
  - Final do OAuth (quando a plataforma devolve um `code`/`state`)
  - Retorno após instalação/ativação
  - Retorno de checkout externo

**Características**:
- Geralmente é uma navegação do browser (GET).
- Você costuma validar parâmetros (ex: `state`) e **trocar `code` por token** no backend.

### ✅ O que é Webhook?
- **Webhook** é uma chamada **servidor → servidor**: a plataforma externa envia um **HTTP POST** para um endpoint do seu backend avisando que “algo aconteceu”.
- **Exemplos comuns**:
  - Pedido criado/atualizado
  - Cliente criado
  - Pagamento aprovado/recusado
  - Alteração de status de assinatura/billing

**Características**:
- Não depende do navegador do usuário.
- Deve ser **rápido** (responder 200/204 e processar assíncrono quando fizer sentido).
- Precisa de **validação/autenticidade** (ex: assinatura/HMAC, token secreto, IP allowlist — depende da plataforma).
- Precisa ser **idempotente** (o mesmo evento pode chegar mais de 1 vez).

---

### 🧩 Como usar nos projetos (Monorepo)

Em geral você vai implementar isso **na API do seu app** (backend):

- **Callback**:
  - Crie/ajuste uma rota pública (ex: `GET /auth/callback`).
  - Valide `state` (anti-CSRF) e leia `code`.
  - Faça a troca do `code` por `access_token` no backend.
  - Persista tokens/dados necessários e redirecione o usuário para o painel/config do app.

- **Webhook**:
  - Crie uma rota pública (ex: `POST /webhooks/nuvemshop` ou `POST /webhooks/<plataforma>`).
  - Valide a origem (assinatura/segredo).
  - Registre o evento (log) e envie para fila/worker (quando existir).
  - Retorne `200 OK` (ou `204 No Content`) o mais rápido possível.

> 💡 Dica: mantenha endpoints de webhook **separados por domínio** (ex: `.../webhooks/billing`, `.../webhooks/orders`) para facilitar troubleshooting e regras de segurança.

---

### 🌐 Quando e por que usar `ngrok`

Quando você está em desenvolvimento local, seu backend geralmente está em `http://localhost:<porta>`. **Plataformas externas não conseguem chamar `localhost`** da sua máquina — por isso você precisa expor uma URL pública temporária.

Use `ngrok` sempre que:
- **Seu app precisar receber Webhooks de plataformas externas** (Nuvemshop, gateways, ERPs, etc.)
- **Você precisar testar Callbacks** (OAuth/instalação) em ambiente local
- **Você estiver lidando com promoções/condições** que dependem de chamadas externas para cálculo/validação

Exemplo de uso (substitua pela porta do seu serviço):

```bash
ngrok http 3122
```

O `ngrok` vai te dar uma URL pública **HTTPS**, algo como `https://<seu-subdominio>.ngrok-free.app`.

Depois disso, você normalmente precisa:
- Atualizar as **URLs de Callback** (quando aplicável) para apontar para a URL do `ngrok`
- Atualizar o destino dos **Webhooks** para apontar para a URL do `ngrok`
- Ajustar variáveis de ambiente do app (ex: `APP_URL`, `API_PUBLIC_URL`, `WEBHOOK_BASE_URL`) se o projeto usar isso

---

### 🏷️ Nuvemshop: promoções (descontos, fretes, etc.) e a necessidade de `ngrok`

Em cenários de **promoções/condições** (por exemplo: lógica de **descontos**, **frete**, validações e regras que a plataforma precisa consultar), seu backend pode precisar receber chamadas vindas “de fora” para:
- Simular/calcular condições
- Validar regras em tempo real
- Receber eventos/atualizações relacionadas à aplicação da promoção

Quando isso acontece em **DEV**, é necessário que o seu serviço esteja acessível publicamente — então **`ngrok` passa a ser obrigatório** para conseguir testar de ponta a ponta.

Checklist rápido em DEV:
- Suba o backend local do app
- Rode `ngrok` apontando para a porta do backend
- Configure a Nuvemshop/integração para usar a URL do `ngrok` nos endpoints (callback/webhook/chamadas externas)
- Teste a funcionalidade (desconto/frete/promoção) e monitore logs do webhook

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