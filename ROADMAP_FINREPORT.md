# 🧭 Roadmap Técnico IA — SaaS de Relatórios Financeiros Automatizados

> **Objetivo:**
> Criar um SaaS de relatórios financeiros que processa extratos CSV/OFX, gera relatórios automáticos (DRE, Fluxo de Caixa, KPIs) e envia por e-mail.
> Stack: **NestJS + Prisma + PostgreSQL + React + Tailwind + Stripe + Docker**

---

## 🩵 FASE 1 — Setup do Projeto e Infraestrutura

**Tags:** `infra` `backend` `frontend` `auth`

### 🎯 Objetivo

Configurar o ambiente base do projeto e autenticação.

### ✅ Tarefas

* [ ] Inicializar backend NestJS + Prisma
* [ ] Criar API Auth + Companies
* [ ] Criar frontend React com login e cadastro
* [ ] Configurar Docker (API, DB, Redis)
* [ ] Integrar Stripe para assinaturas

### 💬 Prompts IA

#### 🔧 Prompt 1 — Backend Base

```
Crie um backend em Node.js com NestJS e TypeScript chamado "finreport-api".
Configure autenticação JWT, conexão com PostgreSQL e CRUD básico de usuários e empresas.
Inclua as entidades: User(id, name, email, password_hash, type), Company(id, user_id, name, created_at).
Implemente endpoints REST /auth/signup, /auth/login e /companies (CRUD).
Use Prisma ORM e gere o schema inicial.
```

#### 🔧 Prompt 2 — Frontend Base

```
Crie um frontend React + Vite + TypeScript chamado "finreport-web".
Adicione TailwindCSS e configure rotas com React Router DOM.
Crie páginas: /login, /signup, /dashboard, /upload.
Integre com a API NestJS /auth/login e /signup.
Mostre loading e erro.
```

#### 🔧 Prompt 3 — Docker

```
Gere docker-compose.yaml com:
- finreport-api (NestJS)
- PostgreSQL com volume persistente
- pgAdmin
- Redis
Inclua Dockerfile do backend e variáveis de ambiente.
```

#### 🔧 Prompt 4 — Stripe

```
Adicione módulo de billing no NestJS com Stripe.
Endpoints:
POST /billing/checkout-session
Webhook /billing/webhook
Entidade Subscription(user_id, plan, status, current_period_end)
Integre frontend para redirecionar ao Stripe Checkout e mostrar status.
```

---

## 💰 FASE 2 — Upload e Processamento de Extratos

**Tags:** `backend` `files` `data-processing`

### 🎯 Objetivo

Permitir upload de extratos CSV e categorização automática.

### ✅ Tarefas

* [ ] Endpoint de upload CSV
* [ ] Parsing e importação no banco
* [ ] Categorização automática (palavras-chave)
* [ ] Processamento em background via Redis

### 💬 Prompts IA

#### 🔧 Prompt 5 — Upload CSV

```
Adicione módulo "Transactions" ao backend.
Implemente POST /transactions/upload com multipart (CSV).
O backend deve ler, detectar colunas, converter para Transaction(company_id, date, amount, description, category_id, source_file) e salvar.
Use csv-parse.
```

#### 🔧 Prompt 6 — Categorização

```
Crie serviço "CategorizationService" com dicionário de palavras-chave.
Exemplo: “mercado” → Alimentação, “pix recebido” → Receita.
Permita editar manualmente via PUT /transactions/:id.
Entidade Category(id, name, type).
```

#### 🔧 Prompt 7 — Worker BullMQ

```
Adicione módulo "Tasks" com BullMQ + Redis.
Fila "transaction-processing".
Cada upload dispara job para categorizar e importar transações.
Crie GET /jobs/:id/status para consultar progresso.
```

---

## 📊 FASE 3 — Geração de Relatórios

**Tags:** `backend` `reports` `pdf` `frontend`

### 🎯 Objetivo

Gerar relatórios automáticos e permitir exportação em PDF.

### ✅ Tarefas

* [ ] Implementar engine de relatórios (Fluxo de Caixa, DRE, KPIs)
* [ ] Exportação PDF
* [ ] Dashboard frontend com gráficos e KPIs

### 💬 Prompts IA

#### 🔧 Prompt 8 — Engine de Relatórios

```
Crie módulo "Reports" no backend.
Implemente GET /reports/:company_id?type=dre&period=YYYY-MM.
Retorne JSON com dados de receitas, despesas, lucro, etc.
```

#### 🔧 Prompt 9 — Exportação PDF

```
Adicione geração PDF com pdfkit.
GET /reports/:id/pdf retorna PDF com layout (logotipo, data, tabela).
Salve em S3 e registre URL no banco.
```

#### 🔧 Prompt 10 — Dashboard

```
Crie tela /dashboard em React:
- Cards com KPIs
- Gráfico de linha (Chart.js)
- Botão “Gerar PDF”
Integre com endpoints /reports.
```

---

## 📬 FASE 4 — Automação e Alertas

**Tags:** `backend` `automation` `email`

### 🎯 Objetivo

Adicionar alertas e relatórios automáticos mensais.

### ✅ Tarefas

* [ ] Implementar sistema de alertas (saldo negativo, despesa alta)
* [ ] Agendador mensal de relatórios
* [ ] Envio automático por e-mail (SendGrid)

### 💬 Prompts IA

#### 🔧 Prompt 11 — Alertas

```
Crie módulo "Alerts".
Entidade Alert(id, company_id, type, threshold, active, last_triggered_at).
Cron job diário com node-cron verifica condições e envia e-mails.
```

#### 🔧 Prompt 12 — Agendamento

```
Crie serviço "Scheduler" que executa mensalmente:
- Gera DRE e fluxo de caixa
- Envia PDF ao dono da empresa
POST /scheduler/test-run força execução manual.
```

---

## ⚙️ FASE 5 — Multiempresa e White-label

**Tags:** `frontend` `backend` `multi-tenant` `customization`

### 🎯 Objetivo

Permitir contadores gerenciar várias empresas e personalizar relatórios.

### ✅ Tarefas

* [ ] Painel multiempresa
* [ ] Templates de relatórios customizáveis
* [ ] Branding (cores, logotipo)

### 💬 Prompts IA

#### 🔧 Prompt 13 — Painel Multiempresa

```
Expanda modelo para suportar User.type = "accountant".
Módulo "Clients" permite adicionar empresas.
GET /accountant/companies retorna resumo financeiro.
Tela /clients exibe lista e KPIs.
```

#### 🔧 Prompt 14 — Templates

```
Crie entidade ReportTemplate(id, company_id, name, layout_json).
Permita POST /templates e uso em /reports/:id/pdf.
Frontend: /templates com editor visual básico.
```

#### 🔧 Prompt 15 — White-label

```
Entidade Branding(company_id, logo_url, color_primary, footer_text).
Aplicar cores e logotipo nos PDFs e painéis.
Permitir contador definir branding padrão.
```

---

## 🔒 FASE EXTRA — Segurança e Observabilidade

**Tags:** `security` `monitoring` `devops`

### 🎯 Objetivo

Adicionar segurança e métricas de performance.

### ✅ Tarefas

* [ ] Rate limiting, Helmet, logs de auditoria
* [ ] Métricas Prometheus + Grafana

### 💬 Prompts IA

#### 🔧 Prompt 16 — Segurança

```
Adicione middleware global de rate limiting, helmet e logs de auditoria (logins, uploads, relatórios).
Tabela AuditLog(user_id, action, timestamp, metadata).
Use Winston logger.
```

#### 🔧 Prompt 17 — Monitoramento

```
Adicione Prometheus no backend e Grafana.
Métricas: api_requests_total, reports_generated_total, job_queue_duration_seconds.
Forneça prometheus.yml e painel grafana.json.
```

---

## 📅 Cronograma Sugerido

| Fase   | Duração   | Entregáveis                    |
| ------ | --------- | ------------------------------ |
| Fase 1 | 2 semanas | Setup backend/frontend, Stripe |
| Fase 2 | 2 semanas | Upload CSV e categorização     |
| Fase 3 | 3 semanas | Relatórios e PDFs              |
| Fase 4 | 2 semanas | Alertas e agendamento          |
| Fase 5 | 3 semanas | Multiempresa e branding        |
| Extra  | Contínuo  | Segurança e métricas           |

---

📘 **Sugestão:**

* Cada **Prompt** pode ser transformado em **Issue** no GitHub Projects.
* Use as labels: `backend`, `frontend`, `infra`, `reports`, `billing`, `security`.
* Vincule milestones às **Fases** para visualização cronológica.
