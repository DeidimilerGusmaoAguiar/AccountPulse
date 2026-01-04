# AccountPulse MVP — Plan

## 1) Arquitetura (alto nível)
- AccountPulse.Web (Blazor Web App + Identity)
- AccountPulse.AccountsApi (Web API)
- AccountPulse.AdvisorApi (Web API)
- AccountPulse.AppHost (Aspire)
- ServiceDefaults (Aspire defaults)

Comunicação:
- Web chama AccountsApi e AdvisorApi via HTTP (REST).
- Não usar mensageria no MVP.

## 2) Dados / Storage
MVP pragmático:
- Identity DB: SQLite (simples) OU LocalDB (se o template vier assim no VS).
- ExchangeAccount:
  - Persistir no AccountsApi (tabela simples) com apiSecret criptografado (Data Protection)
  - Alternativa MVP ultra simples: guardar só em memória (e avisar no README)
Recomendação: persistir com SQLite + EF Core no AccountsApi.

## 3) Segurança
- Secrets:
  - apiSecret criptografado (Data Protection)
  - logs: nunca imprimir request body inteiro; sanitizar
- Auth:
  - Web: cookie auth do Identity
  - APIs: aceitar token/cookie do mesmo app (MVP simples: chamar APIs por server-side do Web)
  - Se usar chamadas diretas do browser: usar JWT/antiforgery/cors (pode ficar fase 2)

## 4) IA (runtime) — MEAI
- AdvisorApi terá 2 modos:
  - rules (default)
  - ai (feature flag Advisor:UseAI=true)
- Implementar um “AI client factory” simples (pode ser direto no AdvisorApi no MVP).
- Config:
  - Ai:BaseUrl (OpenAI-compatible: LiteLLM/Ollama/OpenAI)
  - Ai:ApiKey
  - Ai:Model

## 5) Observabilidade
- Aspire ServiceDefaults:
  - logs estruturados
  - endpoints padrão de health e telemetry
- Correlation:
  - Propagar trace/correlation id entre Web → APIs
- Log policy:
  - request logging com redaction de secrets

## 6) Test strategy
- Unit:
  - Advisor rules engine (OK/WARN/DANGER)
  - JSON contract validator (garantir campos obrigatórios)
- Integration (opcional MVP):
  - /api/advice retorna 200 e formato certo
  - /api/balance retorna payload esperado

## 7) Migrações / Riscos / Rollback
- Risco: Passkeys exigir SchemaVersion 3 e migração do Identity.
  - Mitigação: seguir o template do .NET atual e aplicar migrations.
- Risco: Redirect URI do Google errado.
  - Mitigação: fixar porta do Web no launchSettings no dev.

## 8) Context (para --scope do runner)
Includes:
- src/AccountPulse.Web/**
- src/AccountPulse.AccountsApi/**
- src/AccountPulse.AdvisorApi/**
- src/AccountPulse.AppHost/**
- src/ServiceDefaults/**
- specs/001-accountpulse-mvp/**
Excludes:
- **/bin/**
- **/obj/**
- **/.ai-cache/**
- **/.ai-runs/**
