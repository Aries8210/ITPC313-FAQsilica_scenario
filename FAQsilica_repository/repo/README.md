# Integrated Student Organization Activity Approval and Fund Release Platform

Prefinal project proposal for **ITPC 313 – System Integration and Architecture**
Divine Word College of Legazpi · School of Engineering and Computer Sciences · AY 2026-2027, 1st Semester

**Team FAQsilica**

| Role | Member |
| --- | --- |
| Project Lead / Architect | Jessa Angela Sabueto |
| Integration Developer | Alex Mendoza Jr. |
| Data and Security Lead | Joey Manlapas |
| QA and Documentation Lead | Faith Red |

Instructor: Neal Errol Bulawam

## What this project is

A student organization needs its activity approved and its funds released through several
offices that keep their own records. This project designs one traceable flow that connects:

- **SORS** – Student Organization Registry System (owner: SASO)
- **AAWS** – Activity Approval Workflow System (owner: SASO with college deans)
- **FBS** – Finance and Budget System (owner: Finance Office)
- **SIS** – Student Information System (mocked)
- **Notification Service** (mocked)

Style: an **API gateway** for questions that need an immediate answer plus **event-driven
messaging** through a RabbitMQ broker for approvals, fund release, and notifications.

## Status

Prefinal stage: proposal, design, and contracts only. There is no proof of concept yet; the
build starts in the Final.

## What is in this repository

| Path | Contents |
| --- | --- |
| `docs/openapi.yaml` | Draft OpenAPI 3.1 contract: 12 REST endpoints behind the gateway (proposal Part 9.1) |
| `docs/schemas/` | JSON Schema 2020-12 files: four event schemas and four canonical entities (Parts 9.2 and 9.3) |
| `docs/figures/` | Figure 1 C4 Context, Figure 2 C4 Container, Figure 3 UML deployment, Figure 4 Gantt chart |
| `docs/proposal/` | The proposal PDF and the presentation slides PDF |

## Events

| Event | Producer | Consumers | Routing key |
| --- | --- | --- | --- |
| ActivityApproved | AAWS | FBS, Notification | `activity.approved` |
| FundReleased | FBS | AAWS, Notification | `fund.released` |
| LiquidationSubmitted | FBS | AAWS, Notification | `liquidation.submitted` |
| ProposalStatusChanged | AAWS | Notification | `proposal.status-changed` |

All events use the topic exchange `campus.events`. Every event carries a unique `eventId`
so consumers can ignore repeated messages.

## Conventions

- Base path: `/api/v1`, behind the gateway over HTTPS.
- Authentication: JWT bearer token, 15-minute lifetime; roles are officer, adviser, saso,
  dean, finance, cashier, and admin.
- Money is an integer in PHP centavos; timestamps are ISO 8601 in UTC.

## Data and privacy

Only synthetic or anonymized data is used (RA 10173). Do not commit real student, officer,
or financial data, passwords, tokens, or environment files.

## Planned technology

Node.js with Express, RabbitMQ, PostgreSQL (one database per service), Docker Compose,
Jest and Supertest for integration tests, and k6 for load tests.

## Validating the contracts

```bash
npx @redocly/cli lint docs/openapi.yaml
```
