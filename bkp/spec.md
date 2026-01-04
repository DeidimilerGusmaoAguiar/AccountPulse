# AccountPulse MVP — Spec

## 1) Problema / Objetivo
Quero um projeto piloto que valide a Stack de Desenvolvimento Guiado por IA (BMAD + governança por spec/plan/tasks)
e, ao mesmo tempo, entregue um produto útil e simples: autenticação moderna (Google + MFA + Passkeys),
cadastro de credenciais de uma plataforma (read-only) e um “advisor” que sugere o estado da conta.

## 2) Fluxo principal (MVP)
1. Usuário abre o Web App
2. Faz login com Google (OAuth)
3. Habilita MFA (TOTP + recovery codes)
4. Cadastra uma Passkey (WebAuthn) para login “com biometria do device” (FaceID/TouchID/Windows Hello)
5. Cadastra uma “Exchange Account” (API Key/Secret read-only) — no MVP pode ser mock (sem integrar de verdade)
6. Visualiza saldo/exposição
7. Solicita “Advice” e recebe um JSON curto com status + ações sugeridas

## 3) Escopo (IN)
- Web App com:
  - Login via Google
  - Identity local (usuários)
  - MFA (TOTP) + recovery codes
  - Passkeys (WebAuthn) para login sem senha
  - Tela para cadastrar “Exchange Account” (key/secret)
  - Tela de “Balance” (saldo/exposição)
  - Tela de “Advice” (resultado do advisor)
- AccountsApi:
  - Persistir ExchangeAccount (no MVP: pode persistir apenas metadados + secret criptografado, ou mock)
  - Endpoint de Balance (mock, determinístico)
- AdvisorApi:
  - Endpoint que recebe snapshot e devolve sugestão em JSON
  - Implementação simples + opcional com IA (MEAI) por feature flag
- Aspire:
  - AppHost orquestrando Web + APIs
  - ServiceDefaults habilitando logs/OTel endpoints padrão

## 4) Fora do escopo (OUT)
- Integração real com corretoras (Binance/Bybit/etc)
- Execução de ordens, trading, websockets
- Multi-tenant completo (orgs, subaccounts etc)
- RAG / Semantic Kernel / workflows multi-passos (fica para fase 2)
- Mobile app

## 5) Regras de negócio
- Secrets:
  - Nunca logar API Secret
  - Armazenar secrets criptografados (no MVP pode usar Data Protection; em prod seria KMS/KeyVault)
- Advice:
  - Resposta sempre em JSON validável
  - Se IA falhar (timeout/erro), cair para fallback rule-based (sempre retorna algo)
- Autenticação:
  - Login Google obrigatório para entrada (MVP pode permitir local, mas Google é o caminho feliz)
  - MFA e Passkey são “ativáveis” no perfil (não bloquear login no dia 1)

## 6) Contratos (APIs)
### AccountsApi
- POST /api/exchange-accounts
  - body: { name, apiKey, apiSecret }
  - resp: { id, name, createdAt }
- GET /api/balance?exchangeAccountId=...
  - resp: { totalUsd, assets: [{symbol, amount, usd}], exposureUsd, updatedAt }

### AdvisorApi
- POST /api/advice
  - body:
    {
      "balance": { "totalUsd": 1234, "exposureUsd": 800, "assets": [...] },
      "market": { "btcPrice": 95000, "btcChange24hPct": -2.1, "volatilityHint": "low|med|high" }
    }
  - resp:
    {
      "status": "OK|WARN|DANGER",
      "headline": "string curta",
      "bullets": ["...", "..."],
      "actions": ["...", "..."],
      "source": "rules|ai"
    }

## 7) Critérios de aceite (Definition of Done do MVP)
- Aspire sobe 3 serviços (Web, AccountsApi, AdvisorApi) e abre dashboard
- Login Google funciona no dev (redirect correto)
- Usuário consegue habilitar MFA (TOTP) e gerar recovery codes
- Usuário consegue cadastrar uma Passkey e logar usando ela
- Usuário cadastra ExchangeAccount e consegue ver Balance (mock ok)
- Advisor retorna JSON no formato definido; fallback funciona se IA estiver desligada
- Não há secrets em logs/telemetria
- README com passos pra rodar local (VS + F5 no AppHost)
