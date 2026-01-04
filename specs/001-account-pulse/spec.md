# Feature Specification: Account Pulse MVP

**Feature Branch**: `001-account-pulse`  
**Created**: 2026-01-04  
**Status**: Draft  
**Input**: User description: "AccountPulse: aplicativo simples pra o usuário entrar com Google e depois ver um pulso da conta numa plataforma externa. Requisitos: login Google; exigir MFA no app; permitir biometria via passkeys; tela pra cadastrar credenciais da plataforma (ex: API key/secret ou token); buscar saldo/posições e mostrar de forma resumida; buscar dados públicos de mercado (ex: preço, variação, volatilidade simples); gerar uma sugestão curta do tipo risco alto / risco ok / atenção baseada em regras simples (sem promessas). Foco: MVP útil e pequeno, com logs, erros claros, e sem guardar segredo em texto puro."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Secure sign-in to access the app (Priority: P1)

As a user, I can sign in with my Google account and complete MFA or a passkey so I can access the app securely.

**Why this priority**: Without secure access, no other value can be delivered.

**Independent Test**: Can be fully tested by signing in with a Google account and completing MFA or passkey to reach the app home screen.

**Acceptance Scenarios**:

1. **Given** a user with a valid Google account, **When** they sign in and complete the required MFA or passkey step, **Then** they are granted access to the app.
2. **Given** a user who does not complete the MFA or passkey step, **When** they attempt to continue, **Then** access is denied with a clear error message.

---

### User Story 2 - Connect external platform credentials (Priority: P2)

As a user, I can add or update my external platform credentials so the app can fetch my balances and positions.

**Why this priority**: The account pulse depends on linking the external platform.

**Independent Test**: Can be fully tested by submitting credentials and seeing a successful connection confirmation or a clear error.

**Acceptance Scenarios**:

1. **Given** an authenticated user, **When** they submit API key/secret or token credentials, **Then** the app validates them and confirms the connection.
2. **Given** invalid or expired credentials, **When** the user submits them, **Then** the app rejects them with a clear, actionable error message.

---

### User Story 3 - View account pulse with risk suggestion (Priority: P3)

As a user, I can view a summarized pulse of my account balances and positions along with public market data and a short risk label so I can understand my situation quickly.

**Why this priority**: This is the primary value of the MVP after sign-in and connection.

**Independent Test**: Can be fully tested by loading the pulse screen and verifying balances, positions, market data, and a risk label are shown.

**Acceptance Scenarios**:

1. **Given** valid credentials, **When** the user opens the pulse screen, **Then** the app shows a summarized balance and positions with a last-updated timestamp.
2. **Given** market data is available, **When** the pulse is displayed, **Then** the app shows price, change, and a simple volatility measure.
3. **Given** the pulse is displayed, **When** the app evaluates the account and market data, **Then** it shows one of the labels: "high risk", "risk ok", or "attention" with a short non-promissory note.

---

### Edge Cases

- What happens when the external platform is unavailable or times out?
- How does the system handle missing or partial market data?
- What happens when a user has no positions or zero balance?
- How does the system handle repeated MFA failures or lockout?

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST allow users to sign in with a Google account.
- **FR-002**: System MUST require MFA or a passkey challenge before granting access.
- **FR-003**: System MUST support biometric login via passkeys where available.
- **FR-004**: Users MUST be able to add, update, and remove external platform credentials (API key/secret or token).
- **FR-005**: System MUST validate credentials and provide clear error messages on failure.
- **FR-006**: System MUST fetch balances and positions from the external platform and present them in a summarized view.
- **FR-007**: System MUST fetch public market data including price, change, and a simple volatility measure.
- **FR-008**: System MUST show the last-updated time for the pulse data.
- **FR-009**: System MUST generate a short risk label ("high risk", "risk ok", "attention") based on simple rules and include a non-promissory disclaimer.
- **FR-010**: System MUST log authentication events, credential changes, data fetch failures, and risk label generation.
- **FR-011**: System MUST NOT store external platform secrets in plain text.

### Functional Requirement Acceptance Criteria

- **FR-001**: A user can complete sign-in using a Google account and reach the app home screen.
- **FR-002**: Access is blocked until MFA or passkey challenge is completed.
- **FR-003**: On supported devices, a biometric passkey option is presented and works end-to-end.
- **FR-004**: Users can add, update, and remove credentials in the app UI.
- **FR-005**: Invalid credentials produce a clear, actionable error without saving the connection.
- **FR-006**: A summarized balance and positions view is shown after a successful fetch.
- **FR-007**: The pulse view includes price, change, and a simple volatility measure from public data.
- **FR-008**: The pulse view displays a last-updated timestamp for the data shown.
- **FR-009**: The pulse view shows exactly one risk label and a short non-promissory note.
- **FR-010**: Logs capture auth events, credential changes, fetch failures, and risk label generation.
- **FR-011**: Stored credentials are not readable in plain text by staff or system users.

### Key Entities *(include if feature involves data)*

- **User**: Person using the app with a linked Google account and MFA status.
- **External Credential**: Credential set for the external platform (type, status, last verified).
- **Account Snapshot**: Summarized balances and positions with a timestamp.
- **Market Snapshot**: Public market data points used for the pulse.
- **Risk Label**: One of the three labels plus a short explanation and timestamp.
- **Audit Log Entry**: Recorded security and data fetch events with outcome.

### Assumptions

- The MVP supports a single external platform connection per user at a time.
- Pulse data is refreshed on demand when the user opens the pulse screen.
- Simple volatility is derived from public price movement over a short recent window.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 90% of users can complete sign-in with MFA or passkey in under 2 minutes.
- **SC-002**: 95% of credential submissions receive a success or clear failure response within 30 seconds.
- **SC-003**: 95% of pulse views show balances, positions, and market data within 30 seconds of user request.
- **SC-004**: 90% of users report the risk label is understandable and non-promissory in a post-task survey.
