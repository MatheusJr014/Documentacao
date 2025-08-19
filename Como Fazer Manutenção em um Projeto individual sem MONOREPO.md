
# 📘 Documentação de Manutenção de Aplicativo em Produção (fora de monorepo)

Este guia descreve o passo a passo necessário para configurar o ambiente local, rodar os serviços e realizar manutenção em um aplicativo que já está em produção, mas **não está dentro de um monorepo**.

---

## 📑 Índice
- [🔧 Passo 1 – Clonar os Repositórios](#-passo-1--clonar-os-repositórios)
- [🔄 Passo 2 – Atualizar os Repositórios](#-passo-2--atualizar-os-repositórios)
- [📦 Passo 3 – Instalar Dependências](#-passo-3--instalar-dependências)
- [💻 Passo 4 – Abrir no VS Code](#-passo-4--abrir-no-vs-code)
- [🖥️ Passo 5 – Preparar Terminais](#️-passo-5--preparar-terminais)
- [⚡ Passo 6 – Rodar Redis CLI](#-passo-6--rodar-redis-cli)
- [🚀 Passo 7 – Rodar os Projetos](#-passo-7--rodar-os-projetos)
- [🌐 Passo 8 – Configurar Aplicativo na Nuvem Shop](#-passo-8--configurar-aplicativo-na-nuvem-shop)
- [🎨 Passo 9 – Rodando o FrontStore](#-passo-9--rodando-o-frontstore)
- [🔗 Passo 10 – Como pegar a URL do FrontStore?](#-passo-10--como-pegar-a-url-do-frontstore)
- [📝 Passo 11 – Finalizando](#-passo-11--finalizando)

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

---

## 💻 Passo 4 – Abrir no VS Code
- Abra a pasta `apps` diretamente no **Visual Studio Code**.

---

## 🖥️ Passo 5 – Preparar Terminais
- Dentro do VS Code, abra **4 terminais** (aba `Terminal` → `Novo Terminal`).

---

## ⚡ Passo 6 – Rodar Redis CLI
- Em um dos terminais, inicie o **Redis CLI**:
```bash
redis-cli
```

---

## 🚀 Passo 7 – Rodar os Projetos
- Abra cada pasta em um terminal separado e rode os comandos de inicialização:
  - `middleware-ns-service`
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

## 🌐 Passo 8 – Configurar Aplicativo na Nuvem Shop
1. Acesse o **Painel da Nuvem Shop**.  
2. Vá em **Aplicativo de Teste**.  
3. Configure a **URL local** para apontar para o ambiente de desenvolvimento.

---

## 🎨 Passo 9 – Rodando o FrontStore
1. Acesse:
   - **Parceiros Nuvem Shop** → **Aplicativos**.  
   - Selecione o aplicativo que deseja modificar.  
   - Vá em **Scripts**.  
   - Clique no **lápis (editar)** na última versão.  
   - Coloque em **modo Desenvolvimento**.  
   - Cole a URL do **FrontStore**.

---

## 🔗 Passo 10 – Como pegar a URL do FrontStore?
1. Rode o projeto localmente.  
2. Na raiz, localize o caminho dentro da pasta **dist**:

```
/frontstore-apps/nuvemshop/<NOME_DO_APP>/<NOME_DO_APP.debug.js>
```

3. Adicione o prefixo:
```
http://localhost:9999/
```

👉 Exemplo completo:
```
http://localhost:9999/frontstore-apps/nuvemshop/desconto-por-qtd/desconto-por-qtd.debug.js
```

⚠️ **Atenção:**  
- Em **modo DEV** sempre utilizar **`.debug.js`**.  
- Em **produção**, o sufixo será **`.min.js`**.  

---

## 📝 Passo 11 – Finalizando
- Cole a **URL do FrontStore** no campo de **Modo Desenvolvimento** dentro da Nuvem Shop.  
- Salve as alterações.  
- O app já estará rodando com a versão local.  

---

✅ **Pronto!** Agora você tem todo o ambiente configurado para manutenção do app.
