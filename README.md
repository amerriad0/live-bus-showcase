<div align="center">

<img src="assets/logo.png" alt="Live Bus logo" width="120" />

# 🚍 Live Bus

**Real-time school transportation SaaS platform connecting schools, drivers, and parents.**

[![Flutter](https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white)](https://flutter.dev/)
[![Laravel](https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com/)
[![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black)](https://firebase.google.com/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![SaaS](https://img.shields.io/badge/SaaS-Multi--Tenant-0D9488?style=for-the-badge)](https://livebus.site)

[Website](https://livebus.site) · [Architecture](docs/architecture.md)

</div>

---

## Product Overview

**Live Bus** helps schools and transport operators manage student transportation end to end. The platform combines **GPS tracking**, **mobile applications** for parents and drivers, and **smart administration tools** so organizations can run safer, more transparent routes with less operational friction.

Parents see where the bus is. Drivers run trips with reliable location publishing. Administrators manage people, fleets, finance, and reporting from one dashboard — all within a multi-tenant SaaS model built for organizations at scale.

---

## Main Features

### Parent Application

- Real-time bus tracking
- Live location updates
- Estimated arrival time
- Notifications

### Driver Application

- GPS tracking
- Trip management
- Background location updates
- Route monitoring

### Admin Dashboard

- Student management
- Driver management
- Bus management
- Organization management
- Finance module
- Reports

### SaaS Features

- Multi-tenant architecture
- Subscription management
- Organization accounts
- Role-based access

---

## Tech Stack

| Layer | Technologies |
|-------|----------------|
| **Mobile** | Flutter, Dart |
| **Backend** | Laravel, PHP, REST API |
| **Database** | MySQL, SQL optimization |
| **Real-time** | Firebase Realtime Database |
| **Tools** | GitHub Actions, Docker, Linux |

---

## System Architecture

Live location is streamed through Firebase for low-latency parent updates. Administrative and domain operations go through a Laravel API backed by MySQL.

```
Driver Mobile App
        |
        |
Firebase Realtime Database
        |
        |
Parent Mobile App


Admin Dashboard
        |
        |
Laravel API
        |
        |
MySQL Database
```

For a deeper walkthrough of layers, data flow, authentication, GPS tracking, and multi-tenant design, see **[docs/architecture.md](docs/architecture.md)**.

---

## Screenshots

> Replace the placeholder images below with real product captures.

### Parent App

![Parent App](screenshots/parent-app/image.png)

### Driver App

![Driver App](screenshots/driver-app/image.png)

### Admin Dashboard

![Admin Dashboard](screenshots/admin-dashboard/image.png)

---

## Demo

**Website:** [https://livebus.site](https://livebus.site)

Explore the product experience and learn how Live Bus supports schools, drivers, and parents in one platform.

---

## Repository Scope

This is a **public product showcase** repository. It contains documentation, architecture notes, and media placeholders only.

It does **not** include:

- Application source code
- Backend or Flutter project files
- Environment files (`.env`)
- API keys or Firebase credentials
- Database dumps
- Customer data

---

## Documentation

| Document | Description |
|----------|-------------|
| [Architecture](docs/architecture.md) | System overview, layers, auth, GPS flow, multi-tenant SaaS design |

---

<div align="center">

**Built by Amer Riad**  
Full Stack Developer

[livebus.site](https://livebus.site)

</div>
