# purecodeit-multitenant-skeleton

Este repositório serve como ponto de partida para o **Desafio Técnico** da vaga de
Pessoa Desenvolvedora Fullstack (Laravel + Angular) – Júnior na PureCode IT. Nele,
você encontrará a estrutura básica para desenvolver a **Plataforma de Colaboração
Multi-tenant Simplificada**.

---

## 📂 Estrutura do Projeto

```markdown
purecodeit-multitenant-skeleton/
├── README.md
├── backend/
│   └── .gitkeep
├── frontend/
│   └── .gitkeep
└── docs/
└── schema-diagram.svg
````

- **backend/**: folder onde o projeto Laravel será instalado.
- **frontend/**: folder onde o projeto Angular será instalado.
- **docs/**: contém o diagrama de entidades (DER) em formato de imagem.

---

## 🚀 Como Rodar o Projeto (End-to-End)

1. **Fork e Clone**
   ```bash
   # Faça fork deste repositório para sua conta GitHub
   git clone https://github.com/<seu-usuario>/purecodeit-multitenant-skeleton.git
   cd purecodeit-multitenant-skeleton
    ````

2. **Configurar Backend (Laravel)**

   ```bash
   cd backend
   # Instalar Laravel dentro de backend/
   composer create-project laravel/laravel .

   # Copie o arquivo de ambiente e ajuste as credenciais do Postgres
   cp .env.example .env

   # No .env, configure:
   # DB_CONNECTION=pgsql
   # DB_HOST=db
   # DB_PORT=5432
   # DB_DATABASE=postgres
   # DB_USERNAME=postgres
   # DB_PASSWORD=postgres
   ```

3. **Configurar Frontend (Angular)**

   ```bash
   cd ../frontend
   # Inicialize projeto Angular dentro de frontend/
   ng new .

   # Durante a criação, responda "Yes" para adicionar Angular routing
   # Selecione CSS (ou sua opção favorita)
   ```

4. **Configurar Docker Compose**
   Retorne à raiz do repositório e crie o arquivo `docker-compose.yml`:

   ```yaml
   version: '3.8'
   services:
     app:
       build:
         context: ./backend
         dockerfile: Dockerfile
       volumes:
         - ./backend:/var/www/html
       depends_on:
         - db
       ports:
         - '8000:8000'
       command: >
         sh -c "composer install &&
                php artisan key:generate &&
                php artisan migrate --seed &&
                php artisan serve --host=0.0.0.0 --port=8000"

     db:
       image: postgres:15-alpine
       environment:
         POSTGRES_DB: postgres
         POSTGRES_USER: postgres
         POSTGRES_PASSWORD: postgres
       volumes:
         - pg_data:/var/lib/postgresql/data
       ports:
         - '5432:5432'

     angular:
       image: node:18-alpine
       working_dir: /app
       volumes:
         - ./frontend:/app
       ports:
         - '4200:4200'
       command: >
         sh -c "npm install &&
                npm run start -- --host 0.0.0.0 --port 4200"

   volumes:
     pg_data:
   ```

5. **Executar Containers**

   ```bash
   # Na raiz do projeto
   docker-compose up -d
   ```

6. **Verificar Backend**

    * Acesse `http://localhost:8000/api/tenants`.
    * Deve retornar um JSON (mesmo que vazio, caso não tenha seeders implementados ainda).

7. **Verificar Frontend**

    * Acesse `http://localhost:4200`.
    * Caso o Angular ainda não esteja configurado, exiba a página de boas-vindas do Angular.

---

## 📄 Modelo de Dados (DER)

O diagrama de entidades encontra-se em `docs/schema-diagram.svg`. Ele define:

* **Tenants**
* **Workspaces**
* **Projects**

Cada entidade está relacionada da seguinte forma:

* **Tenant** 1––\* **Workspace**
* **Workspace** 1––\* **Project**

---

## 📋 Tarefas do Desafio

1. **Backend (Laravel)**

    * Criar migrations para as tabelas `tenants`, `workspaces` e `projects`.
    * Definir Models com relacionamentos:

        * `Tenant` *hasMany* `Workspace`
        * `Workspace` *belongsTo* `Tenant`
        * `Workspace` *hasMany* `Project`
        * `Project` *belongsTo* `Workspace`
    * Criar um **Global Scope** no Model `Workspace` que filtre por `tenant_id = 1`.
    * Implementar endpoints:

        * `GET /api/tenants`
        * `GET /api/tenants/{id}/workspaces`
        * `GET /api/workspaces/{id}/projects`
        * `POST /api/workspaces` (recebe `tenant_id`, `name`)
        * `POST /api/projects` (recebe `workspace_id`, `title`, `description`)
    * Criar seeder inicial com:

        * 2 tenants
        * Cada tenant com 1–2 workspaces
        * Cada workspace com 2–3 projects

2. **Frontend (Angular)**

    * Criar `TenantService`, `WorkspaceService` e `ProjectService` para consumir a API.
    * Criar componentes:

        * `TenantListComponent`: lista todos os tenants; ao clicar, carrega workspaces.
        * `WorkspaceListComponent`: lista workspaces de um tenant; ao clicar, carrega projects.
        * `ProjectListComponent`: lista projects de um workspace.
    * Em cada componente, exibir também o nome do “pai” (ex.: em workspaces, mostrar `tenant.name`; em projects, mostrar `workspace.name`).

3. **Documentação**

    * Atualizar o `README.md` com instruções completas de setup.
    * Incluir no diretório `docs/` o DER em formato de imagem (`schema-diagram.svg`).

---

## 📊 Critérios de Avaliação

| Critério                                     | Peso |
| -------------------------------------------- | ---- |
| **Migrations e relacionamentos corretos**    | 25%  |
| **Models e Global Scope implementados**      | 20%  |
| **Qualidade e clareza dos endpoints**        | 20%  |
| **Consumo correto no Angular (nested data)** | 20%  |
| **Estrutura de Docker Compose e README**     | 15%  |

---

> 🚀 **Boa sorte!**
> Este esqueleto foi preparado para guiar seu desenvolvimento. Se tiver dúvidas, entre em contato por e-mail:
> **[eduardo@purecodeit.co](mailto:eduardo@purecodeit.co)**.
