# Live Bus — System Architecture

This document describes the high-level architecture of **Live Bus**, a multi-tenant school transportation SaaS platform. It is intended for technical reviewers, partners, and stakeholders evaluating the product design.

> **Note:** This repository is a public product showcase. Implementation source code is not included.

---

## 1. System Overview

Live Bus connects three primary audiences through a shared platform:

| Role | Client | Primary responsibilities |
|------|--------|---------------------------|
| **Parents** | Flutter mobile app | Track buses in real time, receive arrival estimates and alerts |
| **Drivers** | Flutter mobile app | Run trips, publish GPS location, monitor assigned routes |
| **Admins** | Web dashboard | Manage students, drivers, buses, organizations, finance, and reports |

Core capabilities:

- Real-time GPS tracking via Firebase Realtime Database
- Business logic and persistence via a Laravel REST API and MySQL
- Multi-tenant SaaS tenancy with organization accounts and role-based access
- Subscription-oriented organization onboarding

```
┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
│  Parent App     │         │  Driver App     │         │ Admin Dashboard │
│  (Flutter)      │         │  (Flutter)      │         │  (Web)          │
└────────┬────────┘         └────────┬────────┘         └────────┬────────┘
         │                           │                           │
         │    Live GPS / ETA         │    Location stream        │  REST
         │◄──────────────────────────┼───────────────────────────┤
         │                           │                           │
         ▼                           ▼                           ▼
┌──────────────────────────────────────────┐      ┌──────────────────────────┐
│     Firebase Realtime Database           │      │      Laravel API          │
│     (live location & trip state)         │      │      (business logic)     │
└──────────────────────────────────────────┘      └────────────┬─────────────┘
                                                               │
                                                               ▼
                                                      ┌────────────────┐
                                                      │ MySQL Database │
                                                      └────────────────┘
```

---

## 2. Application Layers

### 2.1 Presentation Layer

- **Parent & Driver apps** — Flutter / Dart clients for iOS and Android
- **Admin dashboard** — Web interface for school and organization operators

### 2.2 Application / API Layer

- **Laravel REST API** — Authentication, CRUD operations, subscriptions, reporting, and administrative workflows
- Stateless HTTP endpoints consumed by mobile and web clients

### 2.3 Real-time Layer

- **Firebase Realtime Database** — Low-latency channel for live bus coordinates, trip status, and parent-facing location updates

### 2.4 Data Layer

- **MySQL** — Durable storage for users, organizations, students, drivers, buses, routes, subscriptions, finance records, and audit-friendly operational data
- Indexed queries and SQL optimization for reporting and multi-tenant filters

### 2.5 Infrastructure & Delivery

- **Docker** — Consistent deployment environments
- **GitHub Actions** — CI/CD pipelines
- **Linux** — Production hosting target

---

## 3. Data Flow

### Operational (admin & domain data)

1. Admin authenticates and selects an organization context.
2. Dashboard calls the Laravel API over HTTPS.
3. API enforces roles and tenant boundaries, then reads/writes MySQL.
4. Responses update the dashboard UI (students, fleets, finance, reports).

### Real-time (location)

1. Driver app obtains GPS fixes (including background updates when permitted).
2. Location payloads are published to Firebase Realtime Database for the active trip.
3. Parent app listens to the relevant trip/bus node and renders the live map, ETA, and status.
4. Trip metadata and historical records remain coordinated through the Laravel API / MySQL where persistence is required.

```
Driver App ──(GPS)──► Firebase RTDB ──(listen)──► Parent App
                │
                └── (trip / domain sync) ──► Laravel API ──► MySQL
```

---

## 4. Authentication Flow

1. User signs in from the Parent app, Driver app, or Admin dashboard.
2. Credentials are validated by the Laravel API.
3. On success, the API issues a session or token used for subsequent REST calls.
4. Role and organization claims determine accessible resources:
   - **Parent** — children, assigned buses/trips, notifications
   - **Driver** — assigned trips, route monitoring, location publishing rights
   - **Admin / org roles** — tenant-scoped management modules
5. Real-time listeners are scoped to trips/buses the authenticated user is allowed to observe.

Unauthorized cross-tenant access is rejected at the API layer.

---

## 5. Real-time GPS Tracking Flow

```
Driver Mobile App
        │
        │  continuous / background location updates
        ▼
Firebase Realtime Database
        │
        │  live location stream
        ▼
Parent Mobile App
```

**Step-by-step:**

1. Driver starts or resumes an active trip in the Driver app.
2. The app streams GPS coordinates (and related trip state) to Firebase.
3. Parents subscribed to that trip receive live map updates and ETA-oriented signals.
4. Notifications can be triggered when arrival thresholds or trip events occur.
5. When the trip ends, publishing stops and final state is reflected for parents and admins.

This split keeps **hot path location traffic** on Firebase while **authoritative business data** stays in Laravel + MySQL.

---

## 6. SaaS Multi-Tenant Design

Live Bus is designed as a **multi-tenant SaaS**:

- Each **organization** (e.g., school or transport operator) is an isolated tenant.
- Users, students, drivers, buses, routes, finance records, and reports are scoped to an organization.
- **Subscription management** controls plan limits and feature access per organization.
- **Role-based access control (RBAC)** restricts modules and actions within a tenant.
- API queries and writes always include tenant context to prevent data leakage between organizations.

### Tenant model (conceptual)

```
Platform
 └── Organization (tenant)
      ├── Subscription / plan
      ├── Users & roles
      ├── Students
      ├── Drivers
      ├── Buses & routes
      ├── Finance
      └── Reports
```

---

## 7. Component Relationship Summary

| Concern | Technology |
|---------|------------|
| Mobile clients | Flutter, Dart |
| Admin UI | Web dashboard → Laravel API |
| Business API | Laravel, PHP, REST |
| Persistent data | MySQL |
| Live GPS | Firebase Realtime Database |
| Automation & ops | GitHub Actions, Docker, Linux |

---

## 8. Security & Showcase Scope

Public materials in this repository intentionally omit:

- Application source code
- Environment files and secrets
- API keys and Firebase credentials
- Database dumps and customer data

For a live demonstration, visit **[https://livebus.site](https://livebus.site)**.

---

*Live Bus — school transportation, managed in real time.*
