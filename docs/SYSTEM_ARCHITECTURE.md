# EduOne — System Architecture

High-level architecture for **EduOne**, a multi-tenant school and kindergarten management SaaS with real-time transportation and academic operations.

> Public showcase documentation only. Source code, credentials, and internal deployment details are not included.

---

## 1. System Overview

EduOne connects role-based clients to shared platform services:

| Role | Client | Responsibilities |
|------|--------|------------------|
| **Parents** | Mobile app | Track buses, view attendance/grades/finance, receive alerts |
| **Drivers** | Mobile app | Run trips, share live GPS, manage onboard attendance |
| **Teachers** | Mobile app | Grades, homework, notes, schedules, class reports |
| **School Admins** | Web portal + mobile admin | Manage students, transport, attendance, finance, academics, settings |
| **Super Admins** | Platform console | Organizations, plans, subscriptions, payments, audit |

```
┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐
│ Parent App │  │ Driver App │  │Teacher App │  │ Admin App  │
└─────┬──────┘  └─────┬──────┘  └─────┬──────┘  └─────┬──────┘
      │               │               │               │
      │               │ Live GPS      │               │
      │               ▼               │               │
      │        ┌──────────────────┐   │               │
      │        │ Firebase Realtime│   │               │
      │        │ Live bus tracking│   │               │
      │        └──────────────────┘   │               │
      │                               │               │
      └───────────────┬───────────────┴───────────────┘
                      │ REST APIs / Auth
                      ▼
               ┌──────────────────┐
               │ Laravel Backend  │
               │ Business logic   │
               └────────┬─────────┘
                        │
                        ▼
               ┌──────────────────┐
               │ MySQL            │
               │ Primary data     │
               └──────────────────┘
```

---

## 2. Application Layers

### Presentation

- Single Flutter codebase with role-based shells (parent, driver, teacher, admin, super admin)
- Laravel Blade web portals for admin, driver tracker, parent map, and super admin

### Application services

- Laravel REST API
- Authentication (Sanctum), authorization (roles + policies), and domain workflows
- Queued jobs for notifications and PDF generation

### Real-time services

- Firebase Realtime Database for live trip location
- Firebase Cloud Messaging (FCM) for push notifications

### Data

- MySQL for durable multi-tenant business data

### Maps & location

- HERE Maps tiles + routing for map views, distance, and ETA

---

## 3. Data Flow

**Operational data** (students, fees, attendance, grades, organization settings) flows through the Laravel API into MySQL.

**Live location** flows from the driver app into Firebase, then to parent and admin clients listening for updates.

```
Driver App ──GPS──► Firebase Realtime ──listen──► Parent / Admin Apps
     │
     └── trip lifecycle & domain sync ──► Laravel API ──► MySQL
```

Laravel still owns:

- Authentication and Firebase custom-token minting
- Trip start / stop lifecycle
- Attendance, finance, and academic business rules
- Notification fan-out

---

## 4. Authentication & Roles

Users sign in with phone + password through the platform API.

Access is scoped by role and organization:

- **Super Admin** — organizations, plans, subscriptions, payments, audit
- **School Admin** — students, parents, teachers, classes, attendance, finance, academics, transport
- **Teacher** — grades, homework, notes, timetable, class reports
- **Driver** — trips, GPS publishing, trip attendance
- **Parent** — children, tracking, attendance, grades, announcements, finance visibility

Cross-organization access is blocked by tenant boundaries.

Organization routes require an active organization/subscription context.

---

## 5. Real-Time GPS Tracking

```
Driver Application
        ↓
Firebase Realtime Database
        ↓
Parent & Admin Applications
```

1. Driver starts an active trip  
2. Location updates are published continuously  
3. Parents and admins see live map updates  
4. Trip-start (and related) notifications are delivered via FCM  

Hot-path location stays on the real-time channel; authoritative business records remain in the API + database.

---

## 6. Multi-Tenant SaaS Design

Each organization (school / kindergarten / academy) is an isolated tenant with its own:

- Users and roles
- Students, parents, and teachers
- Classes, subjects, timetables, and academic periods
- Buses, trips, and routes
- Finance records
- Settings, holidays, and announcements

The platform also supports subscription plans, plan limits, payment review, and audit logs.

```
Platform (Super Admin)
 └── Organization (tenant)
      ├── Subscription + Plan limits
      ├── Users & roles
      ├── Students & classes
      ├── Teachers & academics
      ├── Drivers & buses
      ├── Finance
      └── Reports / announcements
```

---

## 7. Domain Modules (Logical)

| Module | Examples |
|--------|----------|
| **People** | Students, parents, teachers, drivers |
| **Transport** | Buses, trips, QR attendance, home locations, ETA |
| **Attendance** | Daily register + trip attendance |
| **Academics** | Subjects, periods, rooms, timetable, grades, homework, report cards |
| **Finance** | Fees, invoices, payments, receipts, discounts, transport subscriptions |
| **Communication** | Announcements + push notifications |
| **Platform** | Organizations, plans, subscriptions, payments, audit |

---

## 8. Technology Summary

| Area | Stack |
|------|--------|
| Mobile | Flutter, Dart |
| Backend | Laravel, PHP, REST APIs |
| Database | MySQL |
| Auth | Laravel Sanctum, role middleware, policies |
| Real-time | Firebase Realtime Database, FCM |
| Maps | HERE Maps / Routing |
| Delivery | Linux-compatible Laravel deployment (queue + scheduler) |

---

## 9. Showcase Scope

This repository intentionally excludes:

- Application source code
- Environment files and secrets
- API keys and Firebase credentials
- Database dumps and customer data

Demo: [https://eduone.site](https://eduone.site)
