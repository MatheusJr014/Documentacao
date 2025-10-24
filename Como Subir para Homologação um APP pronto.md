# 🧾 Documentação de Homologação de Aplicativos HUAPPS

Este documento descreve **todo o processo de homologação** de um aplicativo desenvolvido no padrão **HUAPPS**, incluindo pré-requisitos, validações, testes, configurações no **Partners da Nuvem Shop** e a solicitação final de homologação.

---

## 📑 Índice

- [✅ Requisitos Prioritários](#-requisitos-prioritários)
- [⚙️ Etapas da Homologação](#️-etapas-da-homologação)
  - [1. Teste e Build Local](#1-teste-e-build-local)
  - [2. Subir o Projeto para Main](#2-subir-o-projeto-para-main)
  - [3. Configuração AWS e URLs de Produção](#3-configuração-aws-e-urls-de-produção)
  - [4. Criação de Diagramas e Vídeo](#4-criação-de-diagramas-e-vídeo)
  - [5. Configuração no Partners](#5-configuração-no-partners)
  - [6. Teste Final e Solicitação de Homologação](#6-teste-final-e-solicitação-de-homologação)
- [📎 Entregáveis Finais](#-entregáveis-finais)

---

## ✅ Requisitos Prioritários

Antes de iniciar o processo de homologação, verifique se todos os **itens prioritários** estão concluídos:

- [ ] APP 100% testado e padronizado no padrão **HUAPPS**  
- [ ] Texto de marketing criado e revisado  
- [ ] Build local realizado com sucesso (`pnpm run build:local`)  
- [ ] Diagrama **UML de Sequência** criado  
- [ ] Vídeo demonstrativo completo do funcionamento do APP  
- [ ] ECS configurado  
- [ ] APP rodando 100% em **produção**  
- [ ] APP totalmente configurado no **Partners da Nuvem Shop**  
- [ ] Script atualizado (versão adicionada, com `min -> Prod` e `debug -> Dev`)  
- [ ] Solicitação de homologação enviada e aguardando retorno da Nuvem Shop  

---

## ⚙️ Etapas da Homologação

### 1. Teste e Build Local

Assim que o APP estiver **completo e rodando em modo local**, é necessário realizar o **teste de build local** para garantir que o código está pronto para produção.

#### 🔧 Configuração do `package.json` (API)
Dentro da pasta **API** do projeto, adicione os scripts abaixo ao arquivo `package.json`, ajustando o nome do app conforme necessário.

```json
"build:local": "tsc --noEmit && echo '✅ TypeScript compilation successful'",
"build:check": "npm run build:local && npm run lint:check",
"copy:ejs": "cpx \"src/templates/**/*.ejs\" dist/apps/compra-rapida/api/src/templates",
"lint:check": "echo '🔍 Checking for common issues...' && npm run check:types",
"check:types": "echo '🔍 Checking TypeScript types...' && tsc --noEmit --skipLibCheck",
"test:build": "npm run build:check && npm run build && echo '✅ Build test successful'"
```

#### ⚛️ Configuração do `package.json` (Front)
Na pasta **front**, adicione também os scripts de build local:

```json
"build": "pnpm --filter @huapps/react-commons build && tsc && vite build",
"build:local": "tsc --noEmit && echo '✅ TypeScript compilation successful'",
"build:check": "npm run build:local && npm run lint:check",
"lint:check": "echo '🔍 Checking for common issues...' && npm run check:types",
"check:types": "echo '🔍 Checking TypeScript types...' && tsc --noEmit --skipLibCheck",
"test:build": "npm run build:check && npm run build && echo '✅ Build test successful'"
```

#### 🧪 Validação
Rode o comando:

```bash
npm run build:local
```

- Corrija os erros que aparecerem.
- Faça commit e push após confirmar que o build passou com sucesso.

---

### 2. Subir o Projeto para Main

Com o projeto totalmente validado e pronto para produção:

```bash
git checkout main
git merge <sua-branch>
git push origin main
```

---

### 3. Configuração AWS e URLs de Produção
Aguarde o responsável pela preparação do servidor AWS.

Assim que o app estiver hospedado e funcional, solicite as URLs de Produção.

Essas URLs serão utilizadas para a configuração do Partners da Nuvem Shop.

---

### 4. Criação de Diagramas e Vídeo

#### 🧩 Diagrama UML (Sequência)
Crie diagramas UML demonstrando o fluxo das requisições feitas à API da Nuvem Shop, detalhando o uso de endpoints como `products`, `categories`, etc.

**Deve ser criado um diagrama para cada tipo de utilização da API.**

📸 **Exemplos:**

![Diagrama UML FrontStore](Images/Diagrama%20UML%20FrontStore.png)

![Diagrama UML Lojista Painel](Images/Diagrama%20UML%20Lojista%20Painel.png)

#### 🎥 Vídeo do APP
Grave um vídeo demonstrando o funcionamento completo do APP, explicando:

- Como ele se relaciona com a API da Nuvem Shop;
- O fluxo de navegação e principais recursos;
- O comportamento em produção.

📹 **Exemplo de vídeo:**

[🎬 Assistir vídeo demonstrativo](https://www.youtube.com/watch?v=9X-BJTzzLf4)

---

### 5. Configuração no Partners

#### ⚙️ Editar Dados Básicos 

- **Informações:** preencher conforme o app.
- **Distribuição:** selecionar **Loja de Aplicativos**.
- **URLs:**
  - **Site do Aplicativo:** CDN do frontend (apenas substituir o slug do app).
  
  ![Exemplo de configuração de URL](https://github.com/MatheusJr014/Documentacao/blob/main/Images/URLs%20Dados%20Basicos.png)
  
  - **URL de Redirecionamento:** mesmo padrão dos outros apps, alterando apenas o slug.
- **Aplicativos incorporados ao administrador:** marcar o checkbox.
- **LGPD:** copiar de outro app já homologado.

#### 📰 Dados de Publicação

- **URL e Contato:** copiar do padrão de outros apps.
- **Handle do aplicativo:** nome do app em kebab-case (ex: `meu-app-teste`).
- **Imagem:** ícone do aplicativo.
- **Idiomas:** configurar todos os idiomas disponíveis.
- **Forma de cobrança:**
  - Marcar como **Gratuito** e habilitar checkbox de vendas dentro do app.
- **Descrição curta e longa:**
  - **Curta** → resumo rápido.
  - **Longa** → descrição formatada conforme o padrão HUAPPS.
- **Ícone:** imagem do app.

#### 💻 Script

Criar novo script personalizado para o app.

**Configurações:**

- **Nome:** FrontStore (ou similar).
- **Local de ativação:** Store.
- **Evento:** `onfirstinteraction`.
- **Carregar versão:**
  - Dist → (Seu App) → `min.js` (Produção).
- **Instalação automática:** marcar o checkbox.
- **Ativar script nas lojas.**

---

### 6. Teste Final e Solicitação de Homologação

#### 🧩 Teste em Produção

- Faça o authorize do app em produção.
- Valide o funcionamento completo:
  - FrontStore;
  - Painel Admin;
  - Comunicação com a API.
- Se tudo estiver estável e fluido, prossiga.

#### 📬 Solicitar Homologação

- Clique em "Solicitar Homologação" no painel Partners.
- O app entrará no estado "Em Aprovação".
- Aguarde o e-mail da equipe da Nuvem Shop solicitando:
  - Diagrama UML;
  - Link do vídeo do APP.

---

## 📎 Entregáveis Finais

- ✅ APP testado e buildado localmente
- ✅ APP rodando em produção (ECS)
- ✅ Diagramas UML enviados
- ✅ Vídeo demonstrativo do APP
- ✅ Configuração completa no Partners
- ✅ Scripts atualizados
- ✅ Solicitação de homologação enviada

