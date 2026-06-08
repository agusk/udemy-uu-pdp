# HelpDeskPro Privacy Portal — Panduan Menjalankan Aplikasi

Dokumen ini menjelaskan cara menjalankan seluruh komponen aplikasi HelpDeskPro Privacy Portal secara lokal, meliputi:

- **Infrastruktur** (PostgreSQL, MinIO, RabbitMQ) via Docker Compose
- **Backend** (.NET 10 API + Worker Service)
- **Frontend** (React + Vite)



## Prasyarat

| Kebutuhan | Versi Minimum |
|---|---|
| Docker Desktop | 24+ |
| .NET SDK | 10 |
| Node.js | 18+ |
| npm | 9+ |



## Struktur Folder

```
codes/
├── docker-compose.yaml              ← Infrastruktur (PostgreSQL, MinIO, RabbitMQ)
├── backend/                         ← .NET Solution
│   ├── HelpDeskPro.slnx
│   ├── src/
│   │   ├── HelpDeskPro.Api/         ← REST API (port 5000)
│   │   │   ├── Controllers/         ← Endpoint controller per fitur
│   │   │   ├── Middlewares/         ← CorrelationId, ErrorHandling
│   │   │   ├── appsettings.json
│   │   │   └── Program.cs
│   │   ├── HelpDeskPro.Infrastructure/  ← EF Core DbContext, Services, Storage, Messaging
│   │   │   ├── Data/                ← Entities, DbContext
│   │   │   ├── Services/            ← AuthService, AuditService, DsrService, dll
│   │   │   ├── Storage/             ← MinIO object storage
│   │   │   ├── Messaging/           ← RabbitMQ publisher
│   │   │   ├── Security/            ← JWT, password hash
│   │   │   ├── Email/               ← SMTP email sender
│   │   │   └── Options/             ← JwtOptions, MinioOptions, RabbitMqOptions
│   │   └── HelpDeskPro.Worker/      ← Background Worker Service
│   │       ├── Workers/             ← DeletionJobWorker, DsrSubmittedWorker, dll
│   │       ├── appsettings.json
│   │       └── Program.cs
│   └── tests/
│       ├── HelpDeskPro.Tests/           ← Unit tests
│       └── HelpDeskPro.IntegrationTests/ ← Integration tests
└── helpdeskpro-react/               ← Frontend React + Vite (port 3000)
```



## Langkah 1 — Jalankan Infrastruktur (Docker Compose)

Masuk ke folder `codes/` lalu jalankan Docker Compose:

```bash
cd codes
docker compose up -d
```

### Layanan yang Dijalankan

| Layanan | Container | Port | Keterangan |
|---|---|---|---|
| **PostgreSQL 14** | `uupdp-db` | `5432` | Database utama |
| **MinIO** | `uupdp-storage` | `9000` (API), `9001` (Console) | Object storage untuk lampiran |
| **RabbitMQ** | `uupdp-rabbitmq` | `5666` (AMQP), `15672` (Management UI) | Message broker |

### Kredensial Default

**PostgreSQL**
```
Host     : localhost:5432
Database : helpdeskpro_pdp
Schema   : helpdeskpro
Username : postgres
Password : pass123
```

**MinIO**
```
Endpoint         : http://localhost:9000
Console UI       : http://localhost:9001
Root User        : admin
Root Password    : pass12345
```
> Setelah MinIO berjalan, buka Console UI dan buat bucket bernama `helpdeskpro`, lalu buat Access Key/Secret Key dan update `appsettings.json` backend.

**RabbitMQ**
```
AMQP Host        : localhost:5666
Management UI    : http://localhost:15672
Username         : admin
Password         : pass123
Virtual Host     : /
```

### Cek Status Container

```bash
docker compose ps
```

### Hentikan Infrastruktur

```bash
docker compose down
```

Untuk menghapus volume (data akan hilang):
```bash
docker compose down -v
```



## Langkah 2 — Install Database dan Seed Data

File database tersedia di folder `db/` dalam bentuk zip.

### 2a. Ekstrak File Database

File zip tersedia di:
```
db/database.zip
```

Ekstrak isinya. Anda akan mendapatkan:
```
db/
├── helpdeskpro_privacy_portal_postgres.sql   ← Schema + struktur tabel
└── helpdeskpro_seed_sql_files/
    ├── 01_reference_data.sql                 ← Roles, purposes, privacy notice
    ├── 02_users_organizations.sql            ← User dan organisasi demo
    ├── 03_consent_records.sql                ← Consent record demo
    ├── 04_tickets_comments_attachments.sql   ← Tiket, komentar, lampiran
    ├── 05_dsr_and_deletion_jobs.sql          ← DSR dan deletion job
    └── 06_audit_and_transfer_records.sql     ← Audit log dan transfer record
```

### 2b. Jalankan Schema Database

Pastikan container PostgreSQL sudah berjalan (`docker compose up -d`), lalu jalankan schema utama:

**Menggunakan psql:**
```bash
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_privacy_portal_postgres.sql
```

**Menggunakan DBeaver / DataGrip:**
1. Buka koneksi ke `localhost:5432` dengan user `postgres` / password `pass123`
2. Pilih database `helpdeskpro_pdp`
3. Buka file `helpdeskpro_privacy_portal_postgres.sql` dan jalankan

### 2c. Jalankan Seed Data (Urut)

Seed data harus dijalankan sesuai urutan nomor file karena ada dependensi antar tabel:

```bash
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/01_reference_data.sql
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/02_users_organizations.sql
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/03_consent_records.sql
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/04_tickets_comments_attachments.sql
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/05_dsr_and_deletion_jobs.sql
psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f db/helpdeskpro_seed_sql_files/06_audit_and_transfer_records.sql
```

**Menggunakan PowerShell (sekaligus):**
```powershell
$files = @(
    "db\helpdeskpro_seed_sql_files\01_reference_data.sql",
    "db\helpdeskpro_seed_sql_files\02_users_organizations.sql",
    "db\helpdeskpro_seed_sql_files\03_consent_records.sql",
    "db\helpdeskpro_seed_sql_files\04_tickets_comments_attachments.sql",
    "db\helpdeskpro_seed_sql_files\05_dsr_and_deletion_jobs.sql",
    "db\helpdeskpro_seed_sql_files\06_audit_and_transfer_records.sql"
)
foreach ($f in $files) {
    Write-Host "Running $f ..."
    psql -h localhost -p 5432 -U postgres -d helpdeskpro_pdp -f $f
}
```

> **Catatan:** Masukkan password `pass123` saat diminta, atau set environment variable `PGPASSWORD=pass123` sebelum menjalankan perintah di atas untuk menghindari prompt berulang:
> ```powershell
> $env:PGPASSWORD = "pass123"
> ```



## Langkah 3 — Jalankan Backend (.NET 10)

### 3a. REST API (HelpDeskPro.Api)

Buka terminal baru, masuk ke folder API:

```bash
cd codes/backend/src/HelpDeskPro.Api
dotnet run
```

API akan berjalan di:
```
http://localhost:5000
```

Swagger UI tersedia di:
```
http://localhost:5000/swagger
```

#### Konfigurasi (`appsettings.json`)

File konfigurasi ada di `backend/src/HelpDeskPro.Api/appsettings.json`. Pastikan koneksi sesuai dengan Docker Compose:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=helpdeskpro_pdp;Username=postgres;Password=pass123;Search Path=helpdeskpro"
  },
  "Minio": {
    "Endpoint": "localhost:9000",
    "AccessKey": "<access-key-dari-minio-console>",
    "SecretKey": "<secret-key-dari-minio-console>",
    "BucketName": "helpdeskpro",
    "UseSsl": false
  },
  "RabbitMq": {
    "Host": "localhost",
    "Port": 5672,
    "Username": "admin",
    "Password": "pass123",
    "VirtualHost": "/"
  }
}
```

> **Catatan Port RabbitMQ:** Docker Compose memetakan port host `5666` → container `5672`. Di konfigurasi backend, gunakan port `5672` (port internal container) jika backend berjalan di host yang sama dengan Docker.

### 3b. Worker Service (HelpDeskPro.Worker)

Buka terminal baru (terpisah dari API):

```bash
cd codes/backend/src/HelpDeskPro.Worker
dotnet run
```

Worker berjalan sebagai background service (tidak ada port HTTP) dan memproses pesan dari RabbitMQ.

### Menjalankan Keduanya Sekaligus (PowerShell)

```powershell
Start-Process powershell -ArgumentList "-NoExit", "-Command", "cd '$PWD\backend\src\HelpDeskPro.Api'; dotnet run"
Start-Process powershell -ArgumentList "-NoExit", "-Command", "cd '$PWD\backend\src\HelpDeskPro.Worker'; dotnet run"
```

### Jalankan Migrasi Database (jika diperlukan)

```bash
cd codes/backend/src/HelpDeskPro.Api
dotnet ef database update
```



## Langkah 4 — Jalankan Frontend (React + Vite)

Buka terminal baru:

```bash
cd codes/helpdeskpro-react
npm install
npm run dev
```

Frontend akan berjalan di:
```
http://localhost:3000
```

Semua request ke `/api/*` akan di-proxy otomatis ke `http://localhost:5000` oleh Vite dev server.

### Build Production

```bash
npm run build
```

Output ada di folder `helpdeskpro-react/dist/`.



## Urutan Startup yang Direkomendasikan

```
1. docker compose up -d                              (infrastruktur)
2. psql ... helpdeskpro_privacy_portal_postgres.sql  (schema database)
3. psql ... 01_reference_data.sql s/d 06_*.sql       (seed data, sekali saja)
4. dotnet run (HelpDeskPro.Api)                      (backend API)
5. dotnet run (HelpDeskPro.Worker)                   (worker service)
6. npm run dev                                       (frontend)
```

> Langkah 2 dan 3 (install database dan seed) hanya perlu dilakukan **sekali** saat pertama kali setup. Selanjutnya cukup mulai dari langkah 1 dan 4.



## Akun Demo

Setelah database seeder berjalan (otomatis saat API pertama kali start), gunakan akun berikut:

| Email | Password | Peran |
|---|---|---|
| `admin@helpdeskpro.id` | `pass123` | SYSTEM_ADMIN |
| `customer@helpdeskpro.id` | `pass123` | CUSTOMER |
| `agent@helpdeskpro.id` | `pass123` | SUPPORT_AGENT |



## Troubleshooting

### Container tidak mau start
```bash
docker compose logs uupdp-db
docker compose logs uupdp-rabbitmq
docker compose logs uupdp-storage
```

### Backend gagal koneksi ke database
Pastikan container `uupdp-db` sudah `healthy`:
```bash
docker compose ps
```
Tunggu beberapa detik setelah container start sebelum menjalankan backend.

### MinIO bucket belum ada
Buka `http://localhost:9001`, login dengan `admin` / `pass12345`, lalu:
1. Buat bucket `helpdeskpro`
2. Buat Access Key baru di menu **Access Keys**
3. Salin Access Key dan Secret Key ke `appsettings.json`

### Port 5432 sudah dipakai
Hentikan PostgreSQL lokal jika ada, atau ubah port mapping di `docker-compose.yaml`:
```yaml
ports:
  - "5433:5432"   # ganti port host
```
Lalu sesuaikan connection string di `appsettings.json`.

### RabbitMQ Management UI tidak bisa diakses
RabbitMQ membutuhkan waktu ~30 detik untuk download plugin `rabbitmq_delayed_message_exchange` saat pertama start. Tunggu hingga container `healthy`.
