# Commercial Proposal for the Development of the CosplayHub System (MVP)

*(Public template version – without client names, concrete prices or confidential details.)*

---

## 1. Project Description

### 1.1. Project Goal

The goal of the project is to design, develop and launch a minimum viable product (MVP) of the **CosplayHub** system: a Telegram‑based news and community hub focused on cosplay with an initial geographical focus on Finland and the Nordic region.

The system should:

* aggregate and normalize content from external sources (primarily RSS feeds);
* publish curated and approved content to a public Telegram channel with an attached discussion chat;
* provide users with basic personalization via a Telegram bot (topics, language, geo, notifications);
* lay the structural foundations for future monetization (partners, campaigns, sponsored content, premium/supporter roles).

### 1.2. Key MVP Tasks

Within the MVP scope it is planned to implement:

* A public **Telegram channel** “CosplayHub” with a linked **discussion chat**.
* A **Telegram bot** for onboarding and configuring user preferences (topics, language, basic geo, notification frequency).
* A **backend orchestrator** based on **n8n** for:

  * automated collection and processing of cosplay‑related news content from RSS and other sources;
  * normalization and storage of content in a relational database;
  * preparation and posting of publications to the Telegram channel;
  * basic moderation (manual review, simple content status workflow).
* A **relational database (PostgreSQL)** with key entities: `User`, `ContentItem`, `Source`, `UserContentInteraction`, `Partner`, `Campaign` and reference tables.
* Technical hooks for further development: user roles, sponsored content flags, partner and campaign identifiers, a basic log of user interactions.

### 1.3. Technologies

* **Telegram Bot API** and Telegram channels/chats – the primary user interface.
* **n8n** – low‑code/no‑code workflow orchestration platform (ingestion, processing, distribution, admin functions).
* **PostgreSQL** – the main relational database engine.
* **Docker / containerization** – deployment of n8n and related services on the client’s server/NAS.
* Optional: **cloud‑based or local LLM services** – for short English summaries and/or content classification.

### 1.4. Deployment Environment

* A server or NAS device controlled by the client with Docker support (e.g. Asustor, Synology or a similar system).
* Deployed containers:

  * n8n (backend orchestrator),
  * PostgreSQL (CosplayHub database),
  * optionally, a reverse proxy (Traefik/nginx) for HTTPS and integration with Telegram webhooks.
* Administrator access to the n8n editor and the database (via DBeaver or a similar client) for monitoring and basic maintenance.

---

## 2. Core System Functionality (MVP)

### 2.1. Telegram Channel and Discussion Chat

* A public **CosplayHub** channel with regular publications of cosplay‑related content focused on Finland and the Nordic region.
* Automatic posting of approved content items (news, events, articles, compilations, creator profiles, etc.) stored in the database.
* The channel is linked to a **discussion chat** where users can comment on posts and communicate with each other.
* Basic setup: channel description, pinned messages with rules and links to the bot.

### 2.2. Telegram Bot (Personalization and Interaction)

* Handling the **/start** command and primary user onboarding.
* Choosing the interface language (**EN/FI**) with the possibility to change it at any time.
* Collecting user preferences:

  * topics of interest (anime, games, events/conventions, tutorials, materials/craft, creators, etc.);
  * basic geo (e.g. “Finland only”, “Finland + Nordics”, “Nordics + global content”);
  * notification/digest frequency (real‑time, daily, weekly – as agreed for the MVP scope).
* Storing the user profile and preferences in the `User` table (PostgreSQL).
* Simple menu interface (buttons or commands), for example:

  * “My settings”,
  * “Change interests”,
  * “Change language”,
  * “Help / About the project”.

### 2.3. Content Pipeline (RSS and Other Sources)

* Configuration of the `Source` table to manage external content sources (RSS/API/manual): type, URL, default language, basic geo/topic tags, polling interval, active status.
* n8n workflow **“RSS – Ingest All Active Sources”** (or equivalent):

  * periodic execution (every N minutes/hours);
  * selection of all active sources with `type = 'rss'` and `is_active = true`;
  * reading RSS feeds for each source;
  * freshness filtering (e.g. last 24 hours);
  * mapping RSS items to source metadata;
  * optional LLM‑based summary (2–3 sentences in English);
  * normalization and insertion into the `ContentItem` table (title, summary, original_url, image_url, language, geo_tags, topic_tags, published_at, status, etc.);
  * updating `last_fetched_at` for processed sources.
* Basic deduplication based on an external ID/URL (via a unique index in the database).

### 2.4. Admin Functions and Moderation

* An administrative Telegram workflow (bot/router) providing:

  * viewing the queue of new items for review (**/review** command);
  * displaying a content card (image, title, short description, source link);
  * inline buttons **Post / Archive / Skip** that update the content status in the database;
  * the **/addsource** command for adding new RSS sources into the `Source` table.
* Content status logic, for example:

  * `new → ready_for_review → approved → posted → archived` (according to agreed rules).
* Use of standard Telegram moderation tools in the discussion chat (ban, mute, delete messages, etc.).

### 2.5. Monetization Mechanics (Structural Level)

* The `ContentItem` table includes fields:

  * `is_sponsored` (sponsored content flag),
  * `partner_id`, `campaign_id` (links to `Partner` and `Campaign` entities),
  * `promo_code` / `tracking_code` for campaign analytics.
* The `User` table includes fields:

  * `role` (user/premium/supporter/partner/admin, etc.),
  * `premium_until`, `supporter_until`,
  * a basic flag to show/hide promotional materials in the future.
* At the MVP stage, these fields can act as placeholders without complex business logic, while providing a foundation for future paid subscriptions, sponsored campaigns and a services marketplace.

### 2.6. Key Technical Details (Architecture and Database)

The architecture is built around the following core components:

* **Telegram channel and chat** – the front‑end for the audience.
* **Telegram bot** – the entry point for personalization, admin commands and interactive features.
* **n8n** – the orchestration layer implementing all business processes (ingestion, processing, review, posting, notifications).
* **PostgreSQL** – a single source of truth for users, content, sources, interactions and partners.

Key tables:

* `User` – Telegram user profile, interests, language, geo preferences, role, notification parameters.
* `Source` – content sources (RSS/API/manual), type, URL, language, geo/topic tags, polling intervals, active status.
* `ContentItem` – normalized content: title, short description, URL, image, language, geo, topics, statuses, monetization flags.
* `UserContentInteraction` – log of user interactions with content (like, save, less, click, etc.).
* `Partner` and `Campaign` – partners and their campaigns linked to content.

---

## 3. Project Implementation

### 3.1. Interview and Requirements Clarification

* Conducting a detailed interview with the client based on the current Technical Specification (MVP version) to clarify MVP priorities, regional focus and initial set of content sources.
* Defining target launch scenarios (conventions/events, news, tutorials, compilations, local shops, etc.).

### 3.2. Preparation and Update of the Technical Specification

* Updating and, if necessary, refining the Technical Specification with regard to:

  * the concrete list of sources;
  * the initial set of tags and topics;
  * requirements for publication frequency;
  * the desired level of automation/manual control.
* Approval of the final TS version as a basis for implementation and cost estimation.

### 3.3. Infrastructure Setup

* Preparing the server/NAS environment:

  * installing and configuring Docker (if needed);
  * deploying n8n and PostgreSQL containers;
  * configuring reverse proxy/HTTPS (if Telegram webhooks are used).
* Configuring access to the n8n editor and the database for the client’s team (where required).

### 3.4. Development and Configuration of n8n Workflows

* Implementing key workflows, including:

  * **RSS ingestion** (reading all active sources, freshness filters, normalization, DB writes);
  * **Content processing/tagging** (basic topic/language/geo logic, using defaults from `Source`, optional LLM classification);
  * **Admin review** (the **/review** command, content cards, Post/Archive/Skip inline buttons, status updates in DB);
  * **Channel posting** (selecting approved/ready items, formatting posts, sending to the channel with photo and link, updating status and `posted_at`);
  * **Admin notifications** (informing about new batches of items in the review queue);
  * basic logging/monitoring (error logging, simple alerts to an admin chat).

### 3.5. Development and Configuration of the Telegram Bot

* Creating and registering the Telegram bot, obtaining a token and connecting it to n8n.
* Implementing logic for:

  * **/start** and onboarding (language choice, topics, geo, notification frequency);
  * viewing and changing settings;
  * service commands (help/about);
  * admin commands (optionally separated into a dedicated admin bot).
* Integrating the bot with the database: reading and writing to the `User` table and interaction logs.

### 3.6. Testing and Launch

* Functional testing of all workflows and the bot:

  * correctness of the ingestion process for key sources;
  * validation of content status changes at each step;
  * verification of post formats in the Telegram channel;
  * testing admin commands and the review process.
* Practical testing on a limited audience / test channel.
* Final production launch with an agreed list of sources and an initial content plan.

---

## 4. Roll‑Out and Handover to the Client

### 4.1. Pilot Operation

* Joint launch of a pilot phase (for example, 2–4 weeks) with daily monitoring of workflow performance.
* Collecting feedback from the client’s team (content quality, frequency, formats, stability).
* Prompt adjustment of rules and settings (filters, tags, publication schedule).

### 4.2. Training and Documentation

* Preparing a short guide for administrators and moderators:

  * how to work with the review queue;
  * how to add new sources;
  * how to use admin commands;
  * basic troubleshooting scenarios.
* An online training session for the client’s team on working with the channel, bot and n8n (if required).

### 4.3. Support (by Separate Agreement)

Possible formats:

* basic warranty support for a limited period after launch (fixing critical defects);
* retainer‑based support (a fixed number of hours per month);
* a package of enhancements (further development within an agreed roadmap).

Specific support terms and scope are defined in a separate addendum or agreement.

---

## 5. Timelines, Budget and Terms

### 5.1. Delivery Timelines

The indicative timeline for implementing the CosplayHub MVP (from TS approval to pilot launch) is to be clarified jointly with the client.

The actual duration depends on factors such as:

* the size of the initial set of sources;
* the depth of onboarding and personalization features required at the MVP stage;
* the number and complexity of integrations with external services (LLM, additional APIs, etc.).

### 5.2. Project Budget

The final project budget for the development and implementation of the CosplayHub MVP is formed individually and depends on the agreed scope of work.

Typically, the budget includes:

* requirements analysis and TS update;
* database and n8n workflow design;
* development and configuration of the bot, channel and discussion chat;
* basic testing and pilot launch;
* preparation of documentation and a short training session for the client’s team.

Additional work (functional extensions, new integrations, long‑term support, monetization features, etc.) is estimated and agreed separately.

### 5.3. Cooperation Terms

* The final evaluation of cost and timelines is prepared after the detailed Technical Specification and list of sources/integrations are agreed.
* A milestone‑based payment model is possible (e.g. advance payment, intermediate payment, final payment after launch). Specific proportions and conditions are defined in the contract.
* The legal form and settlement method (contractor, company‑to‑company, self‑employed, etc.) are subject to separate agreement.

---

## 6. Future Development Potential and Expected Impact

### 6.1. Post‑MVP Development Options

After a successful MVP launch, the CosplayHub system can be extended by:

* introducing personalized digests and recommendations based on user behaviour (`UserContentInteraction`);
* expanding geography (Baltics, wider Europe) by adding new geo tags and sources;
* launching paid roles (premium/supporter) with special perks and closed channels/chats;
* integrating a services marketplace (cosplay commissions, photoshoots, costume and prop production);
* developing partner campaigns and tracking effectiveness (clicks, conversions, promo codes).

### 6.2. Expected Impact for the Client

Implementation of the CosplayHub MVP will enable the client to:

* occupy the niche of a centralized cosplay hub for Finland and the Nordic region;
* reduce manual work related to searching for and publishing news thanks to an automated RSS pipeline;
* build a controlled content flow with transparent review and logging;
* start accumulating audience behaviour data required for future monetization;
* create a technological foundation for scaling the project (new regions, products and partner formats).

---

This commercial proposal template is preliminary and is based on the current MVP Technical Specification for CosplayHub. The final scope of work, timelines and budget are subject to clarification and fixation in a separate agreement after alignment of the detailed Technical Specification and project roadmap.

