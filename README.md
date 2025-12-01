# CosplayHub – Telegram-based Cosplay News & Community Hub (MVP)

CosplayHub is a Telegram-based news and community hub focused on cosplay in Finland and the Nordic region.

This repository documents the **MVP implementation** of the system:
- Technical Specification (TS) and commercial proposal template,
- n8n workflows for content ingestion, moderation and posting,
- the first version of the content pipeline architecture (RSS → DB → Telegram).

It is intended both as a **working project** and as a **portfolio case** for Telegram/n8n/PostgreSQL-based automation.

---

## 1. About the CosplayHub platform

**CosplayHub** is designed as a centralized place for:

- cosplay-related **news, events and updates**,
- tutorials and materials for costume and prop making,
- creator profiles and local convention coverage,
- future partner content and campaigns.

The initial geographic focus is **Finland first**, then **Scandinavia / Nordics**.  
The main user-facing channels are:

- a public **Telegram channel** with regular posts,
- an attached **discussion chat**,
- a **Telegram bot** for personalization and admin interactions.

The long-term goal is to build a flexible platform that can later support:

- sponsored posts and campaigns,
- premium/supporter roles,
- a marketplace for cosplay-related services and commissions.

---

## 2. What this repository contains

### `/docs`

Project documentation and business-level materials, for example:

- **Technical Specification (TS) for CosplayHub MVP** – detailed description of the system, roles, architecture, database structure, workflows and future extensions.
- **Public commercial proposal template (EN)** – a depersonalized proposal describing the scope of work for developing a CosplayHub-style MVP for a client.

These documents show how the project is specified and presented at a professional level.

### `/workflows`

Exports of **n8n workflows** used in the CosplayHub MVP:

- RSS ingestion: reading all active RSS sources, normalizing and storing items in PostgreSQL.
- Content posting: selecting approved items, formatting messages and posting them to the Telegram channel.
- Admin router: handling review commands (e.g. `/review`) and source management (e.g. `/addsource`).

These JSON files can be imported into n8n and adapted for other projects.

### `/logs`

Internal JSON log/snapshot files used during development and debugging of the content pipeline and admin workflows.

They are not required for production but help to understand how the project evolved.

---

## 3. Architecture overview (MVP)

At the MVP stage, CosplayHub consists of:

- **Telegram channel + discussion chat** – the public-facing part where content is published and discussed.
- **Telegram bot** – for onboarding, basic personalization (language, interests, geo) and admin commands.
- **n8n** – low-code/no-code orchestration platform running the main workflows:
  - RSS ingestion,
  - content normalization and storage,
  - moderation via Telegram commands,
  - posting to the channel.
- **PostgreSQL** – the main relational database storing:
  - `User` – Telegram user profiles and preferences,
  - `Source` – content sources (RSS and others),
  - `ContentItem` – normalized content items,
  - `UserContentInteraction` – basic interactions (likes, saves, clicks, etc.),
  - `Partner` and `Campaign` – structures for future monetization.

Deployment is planned on a **self-hosted server/NAS** (e.g. Docker-based environment with n8n and PostgreSQL containers, optionally with a reverse proxy for HTTPS).

---

## 4. Current implementation status

At the current MVP stage, the following pieces are implemented or prepared:

- Database schema for sources, content items, users and interactions.
- n8n workflows for:
  - ingesting content from all active RSS sources,
  - mapping and inserting items into PostgreSQL,
  - selecting items for posting based on status,
  - posting to a Telegram channel and updating status to `posted`.
- An admin workflow (Telegram router) that:
  - responds to admin commands such as `/review` and `/addsource`,
  - shows content cards for review with inline buttons,
  - updates content status in the database.

Automatic content selection and advanced scoring are planned for later, once enough data about relevance and user interactions is accumulated.

---

## 5. Future plans and roadmap (high level)

Planned directions for further development:

- **Smarter content selection**:
  - priority rules,
  - relevance scoring,
  - machine learning/LLM-based classification and ranking.
- **User personalization**:
  - topic and geo-based feeds,
  - personalized digests (daily/weekly),
  - recommendation logic based on `UserContentInteraction`.
- **Monetization features**:
  - partner campaigns and sponsored posts,
  - tracking codes and promo codes,
  - premium/supporter roles with extra perks and closed channels/chats.
- **Geographic expansion**:
  - from Finland to the wider Nordics,
  - possibly to other regions and languages as a next step.

---

## 6. Using this repository as a portfolio case

This repository can be used to demonstrate:

- practical experience with **Telegram Bot API, channels and chats**,
- building automation pipelines with **n8n**,
- designing and using a **PostgreSQL** schema for content-centric systems,
- structuring a real-world MVP with TS, workflows and logs,
- designing for future **monetization and scaling**.

It can serve both as:
- a starting point for building similar systems for other niches, and
- a reference case when discussing cooperation or commercial projects.
# cosplayhub-mvp
Telegram-based cosplay news &amp; community hub for Finland/Nordics (CosplayHub MVP)
