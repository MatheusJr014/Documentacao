# Guia de Configuração de Ambiente (Setup Local)

Este documento descreve os passos necessários para configurar, instalar e rodar o ecossistema do projeto em uma nova máquina de desenvolvimento.

---

## 1. Autenticação e Acesso

Antes de interagir com o código, é necessário garantir que sua máquina tem permissão para acessar os repositórios.

### Configuração de Chave SSH (GitHub)

Para evitar o uso de senhas e garantir segurança via terminal:

- Gere uma nova chave SSH:
  ```bash
  ssh-keygen -t ed25519 -C "seu-email@exemplo.com"
  ```

- Inicie o ssh-agent:
  ```bash
  eval "$(ssh-agent -s)"
  ```

- Adicione a chave ao agente:
  ```bash
  ssh-add ~/.ssh/id_ed25519
  ```

- Copie a chave pública (`cat ~/.ssh/id_ed25519.pub`) e adicione-a em **Settings > SSH and GPG keys** no seu perfil do GitHub.

---

## 2. Inicialização do Projeto

Com o acesso configurado, siga para a obtenção do código.

### Clone do Repositório

Escolha o diretório de sua preferência e execute:

```bash
git clone git@github.com:usuario/nome-do-projeto.git
cd nome-do-projeto
```

### Instalação de Dependências

O projeto utiliza uma estrutura híbrida ou de monorepo que exige múltiplos gerenciadores:

- **NPM:** `npm install`
- **PNPM:** `pnpm install`

---

## 3. Execução e Banco de Dados

O projeto utiliza um Makefile para padronizar comandos complexos.

### Rodar o Ambiente de Desenvolvimento

Execute o comando principal:

```bash
make dev
```

> **Nota:** Se o comando falhar, verifique se as portas necessárias (geralmente 3000, 3333 ou 5432) não estão ocupadas por outros serviços.

### Configuração do Banco de Dados

Certifique-se de que sua instância local (Docker ou Localhost) está ativa:

1. Crie o banco de dados conforme especificado no `.env`.
2. Execute: `pnpm run dev`

---

## 4. Estrutura NS-CORE

O ecossistema é dividido em três pilares fundamentais no banco de dados que precisam estar devidamente populados para o funcionamento do sistema:

- **Apps:** Definições globais das aplicações disponíveis.
- **Store Apps:** Vinculação entre os aplicativos e as lojas específicas.
- **Stores:** Cadastro das lojas/tenants ativos no sistema.

---

## 5. Integração com Postman

Para testar as APIs em modo de desenvolvimento:

1. Importe a Collection do projeto no Postman.
2. Crie um Environment chamado **DEV**.
3. Configure a variável `base_url` (ex: `http://localhost:3000`).
4. Crie um App dentro do ambiente de testes para obter as credenciais de autenticação (Client ID/Secret, se aplicável).

---

## 6. Guia de Troubleshooting (Erro 404: Loja não encontrada)

Caso o sistema suba, mas retorne erro 404 ao tentar acessar uma loja, siga este checklist de depuração:

### Passo A: Verificação de Variáveis

Confira os arquivos `.env`. Certifique-se de que o `DATABASE_URL` e chaves de identificação de ambiente estão corretas.

### Passo B: Integridade das Conexões

Valide se o serviço de API consegue se comunicar com o banco de dados.

### Passo C: Consistência de Dados (Crucial)

O erro 404 geralmente indica que o vínculo no banco de dados está quebrado. Verifique as seguintes tabelas nesta ordem:

| Tabela      | Verificação                                                                 |
|------------|-----------------------------------------------------------------------------|
| **Stores** | A loja que você está tentando acessar existe e o slug ou id coincide com a URL? |
| **Apps**   | O aplicativo core está registrado?                                         |
| **StoreApps** | Existe a relação criada entre o ID da Loja e o ID do App? Sem essa entrada, o sistema não reconhece que a loja possui permissão para usar o recurso. |
