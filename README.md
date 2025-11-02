# server-minimalis

## Deskripsi Singkat
Proyek ini mengorkestrasi Nginx Proxy Manager dengan backend database PostgreSQL menggunakan Docker Compose, menyediakan solusi manajemen proxy yang efisien dan persisten.

## Fitur Utama
- Konfigurasi melalui berkas environment.
- Dukungan orkestrasi via Docker Compose.
- Manajemen proxy HTTP/S yang mudah dengan Nginx Proxy Manager.
- Penyimpanan data persisten untuk Nginx Proxy Manager dan PostgreSQL.

## Prasyarat
Untuk menjalankan proyek ini, Anda memerlukan instalasi berikut di sistem Anda:
- Docker (versi generik)
- Docker Compose (versi generik)

## Quick Start (dengan Docker)
Ikuti langkah-langkah di bawah untuk memulai layanan dengan Docker Compose:

1.  Duplikasi berkas [`.env.example`](.env.example) menjadi `.env` di root proyek dan sesuaikan nilai-nilai variabel lingkungan sesuai kebutuhan Anda. Pastikan untuk mengganti kata sandi default dengan yang kuat.

    ```bash
    cp .env.example .env
    ```

2.  Jalankan perintah Docker Compose untuk menyalakan semua layanan dalam mode detached (berjalan di latar belakang):

    ```bash
    docker compose up -d
    ```

3.  Setelah layanan berjalan, Anda dapat mengakses antarmuka web Nginx Proxy Manager melalui:
    - `http://localhost:81` (untuk antarmuka admin)
    - `http://localhost:80` (untuk lalu lintas HTTP yang akan diproxy)
    - `https://localhost:443` (untuk lalu lintas HTTPS yang akan diproxy)

4.  Untuk mematikan dan menghapus semua kontainer, jaringan, dan volume yang dibuat oleh Docker Compose:

    ```bash
    docker compose down
    ```

## Konfigurasi Environment
Variabel lingkungan yang dapat Anda atur di berkas `.env` Anda:

| Nama             | Deskripsi                                                                                                                                                 | Contoh/Nilai Default                  |
| :--------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------ |
| `POSTGRES_DB`    | Nama database PostgreSQL yang akan dibuat dan digunakan oleh Nginx Proxy Manager.                                                                         | `npm`                                 |
| `POSTGRES_USER`  | Nama pengguna database PostgreSQL yang akan dibuat dan digunakan oleh Nginx Proxy Manager.                                                                | `npm`                                 |
| `POSTGRES_PASSWORD` | Kata sandi untuk pengguna database PostgreSQL. **PENTING: Ganti dengan kata sandi yang kuat dan unik.**                                                       | `ganti_dengan_kata_sandi_yang_sangat_kuat` |

## Layanan dan Orkestrasi (Docker Compose)
Proyek ini mendefinisikan dua layanan utama dalam [`docker-compose.yml`](docker-compose.yml):

### Service: `app` (Nginx Proxy Manager)
- **Image**: `jc21/nginx-proxy-manager:latest`
- **Port Mapping**:
    - `80:80` (HTTP)
    - `443:443` (HTTPS)
    - `81:81` (Web Admin UI)
- **Volumes**:
    - `./data:/data`: Menyimpan data konfigurasi Nginx Proxy Manager.
    - `./letsencrypt:/etc/letsencrypt`: Menyimpan sertifikat SSL/TLS.
- **Environment Keys**: `DB_TYPE`, `DB_POSTGRES_HOST`, `DB_POSTGRES_PORT`, `DB_POSTGRES_USER`, `DB_POSTGRES_PASSWORD`, `DB_POSTGRES_NAME`

### Service: `db` (PostgreSQL Database)
- **Image**: `postgres:18.0-alpine`
- **Volumes**:
    - `./data/postgresql:/var/lib/postgresql/18/docker`: Menyimpan data database PostgreSQL secara persisten.
- **Environment Keys**: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`
