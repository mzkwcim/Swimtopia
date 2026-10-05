# Swimtopia Platform

> Production sports operations platform for athlete management, training
> workflows, reporting and AI-assisted coaching.

## Overview

Swimtopia is a production web platform I design, deploy and operate for
a sports club. It brings athlete data, training workflows, reporting,
strength-training support and selected coaching automations into one
system.

This repository is a **public engineering case study**. The production
source code, infrastructure secrets and athlete data are intentionally
not published.

The project goes beyond building a web application: it requires
operating a real service, integrating systems, designing secure access,
maintaining data flows and creating controlled interfaces for
AI-assisted workflows.

## Problem

Coaching and club operations involve information distributed across
different tools and manual processes. Training data, athlete records,
reports, strength work and technical observations need to remain useful
to coaches without creating additional administrative overhead.

The platform was built to provide a single operational layer where these
workflows can be progressively integrated and automated.

Key goals:

-   centralise athlete and training information;
-   reduce repetitive administrative work;
-   provide coaches with structured reporting and analysis;
-   integrate existing systems rather than rebuild every workflow;
-   keep sensitive athlete information behind controlled access;
-   make automation observable and maintainable;
-   allow AI tools to work through explicitly exposed operations rather
    than unrestricted application access.

## My Role

I am responsible for the platform across multiple layers:

-   architecture and technical decisions;
-   Linux and container infrastructure;
-   deployment and service operation;
-   backend and integration development;
-   PostgreSQL-backed data workflows;
-   identity and access integration;
-   automation;
-   API design and troubleshooting;
-   AI/MCP integration;
-   monitoring and operational troubleshooting.

The system is actively used, so changes need to account for existing
users, data and operational continuity.

## High-Level Architecture

``` mermaid
flowchart TB
    U[Coaches / authorised users]

    subgraph Edge[Access & Edge]
        CF[Cloudflare]
        ACCESS[Identity / Access Controls]
    end

    subgraph Platform[Swimtopia Platform]
        WEB[Web Application]
        API[Application API]
        DB[(PostgreSQL)]
        JOBS[Automation / Background Workflows]
    end

    subgraph AI[AI Integration]
        CHAT[AI Client]
        MCP[MCP Server]
        TOOLS[Controlled MCP Tools]
    end

    subgraph External[External & Club Systems]
        AD[Identity / Directory Services]
        SHEETS[Google Sheets / Data Sources]
        OTHER[Selected External Services]
    end

    U --> CF
    CF --> ACCESS
    ACCESS --> WEB
    WEB --> API
    API --> DB
    API --> JOBS
    CHAT --> MCP
    MCP --> TOOLS
    TOOLS --> API
    API --> AD
    API --> SHEETS
    API --> OTHER
```

The diagram is intentionally simplified and does not expose production
network topology, credentials, internal hostnames or security-sensitive
configuration.

## Core Platform

The application supports club and coaching workflows around athletes and
training data. It has evolved incrementally rather than being treated as
a one-off application.

Examples include:

-   athlete-related records and context;
-   training planning and tracking;
-   coaching observations;
-   reporting and periodic summaries;
-   strength-training workflows;
-   data visualisation;
-   technical/video-analysis workflows;
-   controlled automation exposed to AI tooling.

## Reporting and Athlete Analysis

The reporting layer combines information from multiple parts of the
system to give coaches a useful view of athlete development.

Periodic summaries can include:

-   competition/start information;
-   training completion;
-   development over a selected period;
-   attendance trends where available;
-   coaching observations;
-   conclusions and recommendations.

The goal is not simply to generate text. Reports are built around
structured platform data and coach-controlled workflows.

## Strength Training

Strength training is represented as part of the athlete workflow rather
than as an unrelated external feature.

The platform is being developed to support planned sessions, exercise
and set information, RPE/autoregulation data, session-level feedback,
coach observations, technical analysis and progression over time.

## AI-Assisted Video Analysis

One of the platform workflows supports technical analysis of uploaded
training videos. AI-assisted analysis can provide structured
observations that are incorporated into the coaching workflow.

An important design principle is that AI output is **not treated as an
uncontrolled replacement for the coach**. The system is designed around
reviewable observations and controlled publication/approval flows.

## MCP Integration

The platform exposes selected capabilities through a Model Context
Protocol (MCP) server.

Instead of giving an AI client direct database or unrestricted
application access, the MCP layer exposes purpose-built tools for
defined operations.

``` mermaid
sequenceDiagram
    participant User
    participant AI as AI Client
    participant MCP as MCP Server
    participant App as Swimtopia API
    participant DB as PostgreSQL

    User->>AI: Request an athlete-related operation
    AI->>MCP: Call an allowed tool
    MCP->>MCP: Validate operation and context
    MCP->>App: Execute defined application operation
    App->>DB: Read / update authorised data
    DB-->>App: Result
    App-->>MCP: Structured response
    MCP-->>AI: Tool result
    AI-->>User: Human-readable response
```

This provides a clear boundary between model reasoning and application
capabilities. Tool definitions constrain what the model can request,
while application-side validation remains responsible for enforcing
platform rules.

## Identity and Security

Because the platform contains information related to athletes and club
operations, access control is part of the architecture.

Engineering principles include:

-   authenticated access to production services;
-   least-privilege service access;
-   separation between public interfaces and internal services;
-   controlled API exposure;
-   secrets kept outside source code;
-   explicit AI/MCP tool boundaries;
-   validation of write operations;
-   private connectivity where appropriate.

Some identity and directory integrations exist in production, but their
internal topology and credentials are deliberately excluded from this
repository.

## Infrastructure and Operations

Technologies and concepts used across the project include Linux, Docker
and Docker Compose, PostgreSQL, reverse proxies and HTTP/HTTPS,
Cloudflare services, private connectivity/tunnelling, REST APIs,
Python/FastAPI-based services, Git, monitoring, logs, and
backup/recovery considerations.

Operating the system requires troubleshooting across application,
container, operating-system, network, authentication and data layers.

## Integrations

Swimtopia is designed to integrate with existing workflows rather than
become an isolated application.

Integrations and experiments around the platform include:

-   directory/identity data;
-   Google-based data workflows;
-   external sports/training data;
-   reporting automation;
-   AI clients through MCP;
-   internal APIs and service adapters.

Integration boundaries are kept explicit so external systems can change
without forcing unrelated parts of the platform to be redesigned.

## Selected Engineering Decisions

### Controlled AI access instead of direct database access

AI functionality is routed through explicitly defined MCP tools and
application operations.

**Why:** model reasoning should not automatically imply unrestricted
infrastructure or database permissions.

### API/adaptor boundaries for infrastructure systems

Infrastructure-specific operations are separated behind controlled
service interfaces rather than mixed directly into the web application.

**Why:** this makes authentication, permissions, validation and future
changes easier to reason about.

### Containerised deployment

Application components are deployed as containerised services.

**Why:** reproducibility, dependency isolation and simpler service
lifecycle management.

### PostgreSQL for application data

Structured operational data is stored in PostgreSQL.

**Why:** the platform contains related entities and workflows that
benefit from transactional relational storage.

### Incremental automation

Manual workflows are automated after their operational requirements are
understood.

**Why:** automating an unclear process usually makes it harder to debug
rather than better.

## Operational Challenges

Running the platform has provided practical experience with:

-   container startup and dependency failures;
-   database migration issues;
-   authentication and access failures;
-   API contract and payload problems;
-   networking and reverse-proxy behaviour;
-   data consistency between integrated systems;
-   automation error handling;
-   evolving a live application without breaking existing workflows;
-   separating AI-generated suggestions from operations that modify
    production data.

## What I Learned

1.  **Production ownership changes how software is designed.**
    Deployment, observability, authentication, backups and failure modes
    become part of the feature.
2.  **Integration problems often cross layers.** An apparent application
    bug may originate in DNS, authentication, networking, data shape or
    another service.
3.  **Automation needs guardrails.** Validation, logging, retries and
    clearly defined boundaries matter as much as the happy path.
4.  **AI integrations should expose capabilities, not infrastructure.**
    MCP tools provide a useful boundary, while application-side
    authorisation and validation remain essential.
5.  **Understanding lower layers pays off.** Linux, networking, HTTP and
    identity knowledge repeatedly shorten troubleshooting time.

## Technology Overview

  -----------------------------------------------------------------------
  Area                                Technologies / Concepts
  ----------------------------------- -----------------------------------
  Infrastructure                      Linux, Docker, Docker Compose

  Backend & APIs                      Python, FastAPI, REST APIs

  Data                                PostgreSQL, SQL

  Edge & Access                       Cloudflare, HTTP/HTTPS, private
                                      connectivity

  Automation                          Python, API-driven workflows

  AI Integration                      MCP, controlled tool interfaces,
                                      AI-assisted analysis

  Operations                          Logging, troubleshooting, backups,
                                      service lifecycle

  Development                         Git, containerised
                                      development/deployment
  -----------------------------------------------------------------------

## Repository Scope

This repository contains documentation only.

It intentionally does **not** contain production source code,
credentials or secrets, athlete/user data, production hostnames or
network topology, internal directory structure, private API
configuration, or security-sensitive deployment details.

The purpose is to demonstrate the engineering scope, architecture and
decisions behind the platform without exposing the club's production
environment.

## Status

**Production / actively developed**

The platform continues to evolve as new coaching and operational
requirements are identified.

## Source Code

The production source code is private because the application operates
on real club workflows and potentially sensitive athlete information.

Architecture and implementation details can be discussed at an
appropriate level during a technical interview.
