# CINEFLIX Admin Dashboard Design

**Date:** 2026-03-27
**Project:** CINEFLIX Admin Dashboard
**Stack:** MERN (MongoDB, Express, React, Node.js)

## 1. Overview

CINEFLIX Admin Dashboard is a pure admin/content-management platform for operating the CINEFLIX movie streaming business. It is not a customer-facing streaming application.

The dashboard is designed for a single super admin and uses a Netflix-inspired dark interface. The system focuses on business operations, content curation, user and subscription oversight, homepage/banner management, analytics, and operational visibility into external content synchronization.

Movie and show content comes from TMDB and other external APIs. The admin system does not manage media uploads or raw content creation. Instead, it manages the local control layer that defines how external content is curated, presented, and monitored inside CINEFLIX.

## 2. Goals

- Provide a full admin control center for CINEFLIX operations
- Support one secure super-admin login
- Enable curation of externally sourced movie/show content
- Give visibility into users, subscriptions, analytics, and operational sync health
- Allow homepage and banner configuration without managing media assets directly
- Keep external providers as the source of truth for content data while preserving local editorial overrides

## 3. Product Direction

The selected product direction is a **Curated Ops Dashboard**.

This means the dashboard combines:
- executive/business metrics
- user and subscription administration
- external catalog curation
- storefront presentation controls
- sync operations tooling

This avoids unnecessary CMS complexity, file-upload pipelines, and customer-facing application scope in v1.

## 4. Primary Users

### Single Super Admin

Version 1 is designed for one administrator with full system access.

There is no multi-admin workflow, team collaboration layer, or granular RBAC in v1.

## 5. Visual Direction

The dashboard should use a **Netflix-inspired dark theme**:
- dark surfaces
- high contrast typography
- cinematic poster-led content presentation where appropriate
- red accent color for key actions, states, and brand moments
- clean but premium presentation for analytics and control panels

The overall feel should be premium and operational, not generic SaaS.

## 6. System Architecture

### Frontend

A React single-page admin application with a module-based structure.

Primary frontend areas:
- Dashboard
- Catalog
- Banners/Homepage
- Users
- Subscriptions
- Analytics
- Sync Operations
- Settings

The frontend includes:
- persistent sidebar navigation
- top navigation bar
- KPI surfaces
- tables, filters, forms, drawers, and modals
- notification/alert surfaces for operational issues

State management is split between:
- server state for API-backed data
- local UI state for interaction controls such as filters, dialogs, forms, and table state

### Backend

A Node.js + Express backend exposes authenticated admin APIs.

Backend domains:
- auth
- dashboard analytics
- catalog curation
- homepage/banner management
- users
- subscriptions
- sync operations
- settings

JWT-based authentication protects all admin routes except login.

### Database

MongoDB stores the internal admin data layer.

It stores:
- admin credentials/profile
- local catalog overlay records
- homepage/banner configuration
- platform user records
- subscription records
- sync jobs and sync logs
- operational failure state
- analytics snapshots or cached aggregates where needed

### External Sources

TMDB and other external APIs remain the source of truth for movie and show metadata.

The local system stores only control-layer and operations-layer data, including:
- visibility status
- featured flags
- curated categorization
- custom descriptions
- custom tags
- artwork overrides/references
- homepage placement
- sync timestamps
- sync failures and retry metadata

## 7. Core Modules

### 7.1 Executive Dashboard

The home screen provides a balanced executive + operational overview.

It includes:
- revenue and subscription KPI cards
- active user metrics
- churn indicators
- top-performing content summary
- recent growth signals
- sync failure alerts
- recent admin activity
- quick action entry points

### 7.2 Catalog Curation

This module manages imported content from TMDB and other providers.

Admins can:
- browse synced movies/shows
- filter by source, genre, visibility, featured state, and sync status
- hide or show titles
- mark content as featured
- assign titles to curated categories or homepage sections
- override descriptions, tags, display text, and artwork references
- inspect source metadata and sync metadata

This module does not upload media files or replace source content ownership.

### 7.3 Banner and Homepage Control

This module manages how content appears in the customer-facing storefront.

Admins can control:
- hero banners
- featured rows/carousels
- curated homepage sections
- ordering and prioritization
- publish/unpublish state
- presentation-level content arrangement

### 7.4 Users

This module provides visibility into end users of the streaming platform.

It includes:
- searchable/filterable user list
- account status
- join date
- plan/subscription state
- recent engagement summary
- account control actions such as suspend/reactivate if supported by the platform backend

### 7.5 Subscriptions

This module supports admin-managed subscription oversight.

It includes:
- plan definitions
- subscription statuses
- renewal dates
- manual status adjustments
- subscription history visibility
- business reporting surfaces

Version 1 is admin-managed only and does not require full payment gateway integration.

### 7.6 Sync Operations

This module is the operational control center for external data ingestion.

It includes:
- source/system health overview
- job history
- sync success/failure states
- per-item manual resync actions
- retry actions
- status badges
- failure logs and diagnostic context

This module must make ingestion issues visible without blocking the rest of the dashboard.

### 7.7 Settings

Version 1 settings remain intentionally minimal.

It includes:
- admin profile/password management
- branding values
- source/API configuration placeholders
- dashboard preferences

## 8. Data Flow

1. External APIs such as TMDB provide content metadata.
2. Backend sync services fetch and normalize external data.
3. MongoDB stores the local overlay/configuration/operations layer.
4. React admin pages consume Express APIs rather than calling external providers directly.
5. Admin edits update only local overlay records, not the external source records.
6. Dashboard analytics combine internal business data with curated content and sync health summaries.

## 9. Error Handling

The system should fail gracefully and visibly.

Requirements:
- external provider failures must not break the full admin panel
- sync failures must surface clearly in Sync Operations and relevant alert areas
- catalog views should continue showing last known local/synced state when sources are temporarily unavailable
- all admin actions must return clear success/error feedback
- authentication failures must redirect cleanly to login
- backend failures must be logged with actionable context

## 10. Testing Strategy

### Unit Tests
- auth utilities
- catalog curation logic
- analytics aggregation helpers
- sync status transformation logic

### Integration Tests
- login/auth routes
- catalog curation APIs
- subscriptions APIs
- banner/homepage APIs
- sync operations endpoints

### End-to-End Tests
- admin login flow
- dashboard initial load
- content curation workflow
- banner/homepage editing workflow
- sync retry workflow

External providers should be mocked at the boundary for deterministic tests.

## 11. Scope for Version 1

### Included
- single-admin authentication
- executive analytics dashboard
- content performance visibility
- catalog curation for external content
- homepage/banner management
- user management visibility
- subscription management
- sync operations tooling
- Netflix-inspired dark UI

### Excluded
- customer-facing streaming application
- multi-admin collaboration
- granular roles and permissions
- full payment gateway integration
- media upload/storage pipeline
- full CMS/page builder
- complex editorial approval workflows

## 12. Recommended Build Order

1. Project foundation and MERN app structure
2. Admin authentication
3. Shared admin layout/navigation/theme
4. Dashboard KPI overview
5. Catalog curation module
6. Banner/homepage management
7. Users module
8. Subscriptions module
9. Sync operations module
10. Settings and final polish
11. Full testing and stabilization

## 13. Success Criteria

The v1 dashboard is successful if the super admin can:
- log in securely
- understand business and content performance at a glance
- curate imported movies/shows without managing raw media files
- control homepage banners and curated sections
- review users and subscriptions effectively
- monitor external sync health and recover from failures
- operate the platform from one cohesive admin interface
