# Implementasi UU PDP: Aplikasi, API, Database dan Keamanan

> **Course Udemy** · Panduan teknis membangun sistem siap UU PDP: consent, audit log, hapus data, backup, secure storage, dan kontrol akses.

[![Udemy](https://img.shields.io/badge/Udemy-Course-purple?logo=udemy)](https://www.udemy.com/course/uupdp-ti/?referralCode=B2D0DC9D5653F93EA2C7)
[![Stack](https://img.shields.io/badge/Stack-.NET%2010%20%7C%20React%20%7C%20PostgreSQL-blue)](#tech-stack)
[![License](https://img.shields.io/badge/License-MIT-green)](#)

---

## Daftar Isi

- [Tentang Course](#tentang-course)
- [Mengapa Course Ini Penting](#mengapa-course-ini-penting)
- [Studi Kasus: HelpDeskPro Privacy Portal](#studi-kasus-helpdeskpro-privacy-portal)
- [Tech Stack](#tech-stack)
- [Silabus — 10 Section](#silabus--10-section)
- [Source Code & Release](#source-code--release)
- [Cara Menjalankan Aplikasi](#cara-menjalankan-aplikasi)
- [Daftar Sekarang](#daftar-sekarang)

---

## Tentang Course

**[🎓 Implementasi UU PDP: Aplikasi, API, Database dan Keamanan](https://www.udemy.com/course/uupdp-ti/?referralCode=B2D0DC9D5653F93EA2C7)**

Course ini dirancang khusus untuk **developer, DBA, DevOps, dan IT security** yang perlu memahami dan mengimplementasikan **Undang-Undang Pelindungan Data Pribadi (UU PDP)** secara teknis di dalam sistem perangkat lunak nyata.

Berbeda dari course hukum atau compliance, course ini sepenuhnya fokus pada **implementasi teknis**:

- Bagaimana merancang **database** yang mendukung consent, audit, retention, dan deletion.
- Bagaimana membangun **REST API** yang mengatur akses data sesuai prinsip UU PDP.
- Bagaimana menerapkan **secure storage**, hashing, masking, dan pre-signed URL.
- Bagaimana membuat **audit trail** yang membuktikan kepatuhan.
- Bagaimana menjalankan **Data Subject Request (DSR)** end-to-end melalui workflow asinkron.
- Bagaimana menyiapkan sistem untuk **incident response** dan **production readiness**.

**Pendekatan pembelajaran:** Pre-built final project → guided code walkthrough → focused hands-on lab → cek database/storage/log → recap kontrol UU PDP.

> **10 section** · Studi kasus aplikasi nyata (HelpDeskPro Privacy Portal)

---

## Mengapa Course Ini Penting

UU PDP berlaku di Indonesia dan mewajibkan setiap pengendali data pribadi untuk menerapkan perlindungan teknis yang memadai. Bagi tim IT, ini berarti:

| Tantangan | Solusi di Course Ini |
|---|---|
| Tidak tahu tabel/field mana yang menyimpan data pribadi | Data inventory & classification |
| Consent hanya disimpan sebagai boolean | Consent management berbasis purpose & versioning |
| Tidak ada audit trail akses data | Structured audit log & correlation ID |
| Tidak ada mekanisme hapus data | DSR workflow + deletion job asinkron |
| File attachment tidak diproteksi | Secure storage dengan MinIO pre-signed URL |
| Tidak ada retention policy | Data retention + anonymization job |
| Tidak tahu vendor mana yang menerima data | Vendor inventory & data transfer matrix |

---

## Studi Kasus: HelpDeskPro Privacy Portal

Seluruh course menggunakan **HelpDeskPro Privacy Portal** sebagai aplikasi contoh — sebuah *Customer Support & Service Request Portal* yang dirancang dengan prinsip **Privacy by Design**.

### Skenario yang Dicakup

| Area | Contoh |
|---|---|
| B2C | Customer membuat tiket komplain/service request |
| B2B | Perusahaan/tenant memiliki user yang membuat tiket |
| Internal App | Agent, supervisor, admin, auditor mengelola tiket dan audit |
| Privacy Workflow | Consent, Data Subject Request, audit log, deletion, retention, vendor transfer |

### Aktor Sistem

- **Customer** — Subjek data, membuat tiket, mengelola consent, mengajukan DSR
- **Support Agent** — Memproses tiket, akses terbatas dan ter-audit
- **Supervisor** — Review & approve DSR, monitoring audit log
- **System Admin** — Konfigurasi sistem, manage user
- **Auditor** — Read-only akses ke seluruh audit log

---

## Tech Stack

| Layer | Teknologi |
|---|---|
| Frontend | ReactJS + Vite |
| Backend API | ASP.NET Core Web API (.NET 10) |
| Background Service | .NET Worker Service |
| Database | PostgreSQL 14 |
| ORM | Entity Framework Core (Database First) |
| Object Storage | MinIO (S3-compatible) |
| Message Broker | RabbitMQ |
| API Testing | Swagger UI + Postman |
| Lab Environment | Docker Compose |
| IDE | Visual Studio Code |

---

## Silabus — 10 Section

### Section 1 — Pengenalan UU PDP untuk Tim IT
Memahami UU PDP dari perspektif teknis: subjek data, pengendali, prosesor, jenis data pribadi, hak subjek data, dan dampaknya ke sistem IT. Walkthrough studi kasus HelpDeskPro.

### Section 2 — Data Inventory, Data Classification, dan Privacy by Design
Cara membuat data inventory, klasifikasi risiko per field, mapping data ke purpose & retention, dan prinsip privacy by design dalam desain arsitektur.

### Section 3 — Database Design untuk Implementasi UU PDP
Desain tabel untuk consent, privacy notice, Data Subject Request, audit log, retention policy, deletion job, dan third-party processor. Termasuk lab menjalankan schema PostgreSQL dan ERD walkthrough. Bonus: mapping ke MySQL, SQL Server, Oracle, dan MongoDB.

### Section 4 — Consent Management dan Privacy Notice
Implementasi consent berbasis purpose, privacy notice versioning, re-consent, dan API consent di ASP.NET Core. Lab: grant & withdraw consent via Swagger/Postman + demo UI ReactJS.

### Section 5 — Data Subject Request: Akses, Koreksi, Pembatasan, dan Penghapusan Data
Desain workflow DSR end-to-end (SUBMITTED → COMPLETED), implementasi API, dan lab praktik untuk access request, correction, withdraw consent, dan deletion request.

### Section 6 — Secure Storage: Hashing, Encryption, Masking, dan File Upload
Hashing password dengan bcrypt, JWT, secret management, masking data di UI & log, dan secure file upload ke MinIO dengan pre-signed URL. Lab: upload & download attachment tiket.

### Section 7 — Logging, Audit Trail, dan Monitoring Akses Data Pribadi
Perbedaan application log vs audit log, desain structured audit log, correlation ID, dan lab audit saat agent membuka tiket, download attachment, dan withdrawal consent.

### Section 8 — Retention, Deletion, Anonymization, Backup, dan Restore
Retention policy per jenis data, soft delete vs hard delete vs anonymization, asynchronous deletion job via RabbitMQ + .NET Worker, backup PostgreSQL, restore, dan masking data di environment test.

### Section 9 — Data Transfer, Vendor, Cloud, dan DevOps Security
Vendor inventory, data transfer matrix, cloud storage & region, DevOps control via Docker Compose, environment secret, database & API security checklist, IDOR prevention, dan rate limiting.

### Section 10 — Incident Response dan Final Project PDP-Ready Application
Simulasi insiden kebocoran data dari log dan misconfiguration object storage, review final project end-to-end, production readiness checklist 10 layer, dan roadmap implementasi UU PDP di organisasi.

---

## Source Code & Release

**Source code, SQL schema, seed data, dan file pendukung tersedia di [GitHub Releases](../../releases) project ini.**

Unduh release terbaru untuk mendapatkan:

```
release/
├── codes/                              ← Source code lengkap
│   ├── docker-compose.yaml
│   ├── backend/                        ← .NET 10 Solution
│   │   └── src/
│   │       ├── HelpDeskPro.Api/
│   │       └── HelpDeskPro.Worker/
│   └── helpdeskpro-react/              ← Frontend React + Vite
└── db/
    ├── database.zip                    ← Schema + seed data PostgreSQL
    └── helpdeskpro_seed_sql_files/
```

---

## Cara Menjalankan Aplikasi

Lihat panduan lengkap instalasi dan konfigurasi di [Get-Started.md](Get-Started.md), mencakup:

- Prasyarat (Docker Desktop, .NET SDK, Node.js)
- Menjalankan infrastruktur via Docker Compose (PostgreSQL, MinIO, RabbitMQ)
- Install schema dan seed data PostgreSQL
- Menjalankan Backend API dan Worker Service (.NET 10)
- Menjalankan Frontend (React + Vite)
- Urutan startup yang direkomendasikan
- Akun demo dan troubleshooting

---

## Daftar Sekarang

Pelajari cara membangun sistem yang benar-benar siap UU PDP — dari desain database, API, consent management, audit trail, hingga incident response.

**[🎓 Enroll Course di Udemy →](https://www.udemy.com/course/uupdp-ti/?referralCode=B2D0DC9D5653F93EA2C7)**

---

*Repository ini berisi source code pendamping course Udemy **"Implementasi UU PDP: Aplikasi, API, Database dan Keamanan"**. Source code lengkap tersedia di bagian [Releases](../../releases).*
