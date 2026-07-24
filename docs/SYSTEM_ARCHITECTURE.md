# Live Bus — System Architecture

High-level architecture for **Live Bus**, a multi-tenant school and kindergarten management SaaS with real-time transportation.

> Public showcase documentation only. Source code, credentials, and internal deployment details are not included.

---

## 1. System Overview

Live Bus connects three client experiences to shared platform services:

| Role | Client | Responsibilities |
|------|--------|------------------|
| **Parents** | Mobile app | Track buses, receive alerts, view attendance and finance |
| **Drivers** | Mobile app | Run trips, share live location, manage onboard students |
| **Admins** | Admin app / dashboard | Manage students, transport, attendance, finance, and organization settings |

```
┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│  Parent App  │   │  Driver App  │   │  Admin App   │
└──────┬───────┘   └──────┬───────┘   └──────┬───────┘
       │                  │                  │
       │     Live GPS     │     REST APIs    │
       ▼                  ▼                  ▼
┌──────────────────┐              ┌──────────────────┐
│ Firebase Realtime│              │  Laravel Backend │
│ Live tracking    │              │  Business logic  │
└──────────────────┘              └────────┬─────────┘
                                           │
                                           ▼
                                  ┌──────────────────┐
                                  │      MySQL       │
                                  │   Primary data   │
                                  └──────────────────┘
```

---

## 2. Application Layers

### Presentation

- Flutter mobile clients for parents and drivers
- Admin application for school and platform operators

### Application services

- Laravel-based backend exposing REST APIs
- Authentication, authorization, and domain workflows

### Real-time services

- Firebase Realtime Database for live trip location
- Firebase Cloud Messaging for push notifications

### Data

- MySQL for durable multi-tenant business data

### Maps & location

- HERE Maps and routing services for map experiences

---

## 3. Data Flow

**Operational data** (students, fees, attendance, organization settings) flows through the Laravel API into MySQL.

**Live location** flows from the driver app into Firebase, then to parent and admin clients listening for updates.

```
Driver App ──GPS──► Firebase Realtime ──listen──► Parent / Admin Apps
     │
     └── trip & domain sync ──► Laravel API ──► MySQL
```

---

## 4. Authentication & Roles

Users sign in through the platform API. Access is scoped by role and organization:

- **Super Admin** — organizations, subscriptions, platform operations
- **School Admin** — students, parents, classes, attendance, finance, transport
- **Driver** — trips, GPS publishing, student attendance on trip
- **Parent** — children, tracking, notifications, attendance, finance visibility

Cross-organization access is blocked by tenant boundaries.

---

## 5. Real-Time GPS Tracking

```
Driver Application
        ↓
Real-Time Location System (Firebase)
        ↓
Parent & Admin Applications
```

1. Driver starts an active trip  
2. Location updates are published continuously  
3. Parents and admins see live map updates  
4. Notifications support arrival and trip events  

Hot-path location stays on the real-time channel; authoritative business records remain in the API + database.

---

## 6. Multi-Tenant SaaS Design

Each organization (school / kindergarten) is an isolated tenant with its own:

- Users and roles
- Students and parents
- Buses and routes
- Finance records
- Settings and announcements

The platform also supports subscription plans and organization account management.

```
Platform
 └── Organization (tenant)
      ├── Subscription
      ├── Users & roles
      ├── Students & classes
      ├── Drivers & buses
      ├── Finance
      └── Reports
```

---

## 7. Technology Summary

| Area | Stack |
|------|--------|
| Mobile | Flutter, Dart |
| Backend | Laravel, PHP, REST APIs |
| Database | MySQL |
| Real-time | Firebase Realtime Database, FCM |
| Maps | HERE Maps |
| Delivery | GitHub Actions, Docker, Linux |

---

## 8. Showcase Scope

This repository intentionally excludes:

- Application source code
- Environment files and secrets
- API keys and Firebase credentials
- Database dumps and customer data

Demo: [https://livebus.site](https://livebus.site)
