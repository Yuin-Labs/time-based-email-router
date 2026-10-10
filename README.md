# Time-Based Email Router & Dispatcher

🌍 **[English](#english-version) | 🇮🇩 [Bahasa Indonesia](#versi-indonesia)**

---

## 🇬🇧 English Version

Automated Microsoft Power Automate flow designed to inspect incoming service/travel requests, apply sender filtration, and dynamically route emails based on operational hours (Working Hours, After Hours, and Weekends).

### 📌 Problem Statement
Manual processing of incoming travel and operational requests often creates severe bottlenecks and delays, especially outside standard working hours. Without an automated routing system, urgent requests sent during weekends or after-hours are frequently missed or delayed until the following business day. This system eliminates manual intervention by intercepting emails, filtering out system noise, and routing them to the correct SLA-bound tier.

### 📐 Logic Flow & Architecture

[Incoming Email]
       │
       ▼
[Exclusion Filter] ─── (Match System/Internal Emails) ───> [Terminate Flow]
       │
       ▼
[Timezone Conversion to Local Time (UTC+7)]
       │
       ├── Weekend Check (Sat-Sun) ─────────> Route to Weekend Support Queue
       │
       └── Weekday Check (Mon-Fri)
              ├── Working Hours (08:00 - 17:00) ──> Route to Primary Support Queue
              └── After Hours (17:01 - 07:59)   ──> Route to 24/7 On-Call Support


### ✨ Key Features

* **Sender Exclusion Guard:** Automatically filters out system-generated emails, postmasters, and internal automated notifications to prevent infinite loops.
* **Timezone Standardization:** Converts UTC timestamps dynamically into local working timezone (`SE Asia Standard Time` / UTC+7).
* **Dynamic Routing Engine:**
* **Working Hours (Mon–Fri, 08:00–17:00):** Directs incoming requests to the primary operations counter.
* **After Hours (Mon–Fri, 17:01–07:59):** Dual-routes requests to both the primary counter and the 24/7 emergency support team, auto-CCing original senders.
* **Weekends (Saturday–Sunday):** Routes requests directly to the dedicated weekend on-call team.


* **Attachment Retention:** Re-fetches the full email payload to preserve and forward all original attachments without data loss.

### 🛠️ Tech Stack & Components

* **Platform:** Microsoft Power Automate
* **Connectors:** Office 365 Outlook API
* **Logic Functions:** Expression-based timezone conversion, Day-of-Week evaluation, and conditional branching logic.

### 🚀 Setup & Deployment

This repository contains a flow configuration that must be tailored before running in a production environment.

**1. Deployment**

* Open the Power Automate portal, go to **Solutions** or **My flows**, and select **Import**.
* Upload the release package containing the manifest and flow definition.
* Re-bind the **Office 365 Outlook** connection to a dedicated service account with full access to the operational mailbox. Do not use a personal account.

**2. Core Parameter Configuration**
Edit the flow and update the following nodes to match your environment:

* **Cutover Timestamp:** In the go-live gate node, set the operational start date/time (`YYYY-MM-DDTHH:mm:ss`).
* **Timezone:** Update the `Convert time zone` action and `LocalTime` Compose node if operating outside `SE Asia Standard Time`.
* **Sender Filters:** Adjust exact match and partial keyword lists to ignore internal system noise.
* **Partner Domain:** Set the agency/partner domain to prevent auto-reply loops.
* **Mailbox Validation:** Insert your actual central inbox address in the target validation node so BCC/distribution list noise is dropped.
* **Target Recipients:** Update the standard counter and 24/7 desk email addresses in the `Reply to email` nodes.

**3. Test Mode**
Simulate different times of the week without waiting for the actual clock to tick by overriding expressions in the Compose nodes:

* **DayNumber:** Replace the expression with a static integer (0 for Sunday to 6 for Saturday).
* **CurrentHour:** Replace the expression with a static integer (0 to 23).
* *Crucial:* Use your own test email address for target recipients to avoid spamming external partners during tests.
* *Cleanup:* Revert `DayNumber` and `CurrentHour` back to their dynamic expressions after testing.

**4. Pre-Production Checklist**
Validate these before going live:

* [ ] Connection is bound to the service account.
* [ ] Go-live cutover timestamp is valid.
* [ ] All test email addresses have been replaced with real production targets.
* [ ] Static test numbers in `DayNumber` and `CurrentHour` have been removed and reverted to formulas.
* [ ] Failure alert subscriptions are routed to the system maintainer's account.

---

---

## 🇮🇩 Versi Indonesia

Alur kerja Microsoft Power Automate tanpa antarmuka (headless) untuk menginspeksi, memfilter, dan merutekan email permintaan operasional secara otomatis berdasarkan jam kerja, luar jam kerja, dan akhir pekan.

### 📌 Latar Belakang

Permintaan operasional yang dikirim ke kotak masuk sentral sering mengalami penundaan saat masuk di luar jam kerja. Sistem ini mengeliminasi intervensi manual dengan mencegat email masuk, memfilter pengirim otomatis atau sistem, dan meneruskannya ke tujuan yang tepat sesuai tenggat waktu SLA operasional yang berlaku saat itu.

### 📐 Arsitektur & Logika Routing


[Email Masuk]
       │
       ▼
[Filter Pengirim] ─── (Domain Sistem/Bot) ───> [Hentikan Eksekusi]
       │
       ▼
[Konversi Waktu UTC ke Lokal]
       │
       ├── Cek Akhir Pekan (Sab-Min) ──────> Rute ke Tim Siaga Akhir Pekan
       │
       └── Cek Hari Kerja (Sen-Jum)
              ├── Jam Kerja (08:00 - 17:00) ──> Rute ke Loket Utama
              └── Luar Jam (17:01 - 07:59)  ──> Rute ke Loket Utama & Tim Siaga 24/7


### ✨ Fitur Utama

* **Pengecualian Pengirim Otomatis:** Memblokir email dari sistem internal dan domain pihak ketiga untuk mencegah *infinite loop*.
* **Konversi Zona Waktu Dinamis:** Mengubah timestamp UTC menjadi waktu operasional standar (`SE Asia Standard Time` / UTC+7).
* **Routing 3 Jalur:**
* **Jam Kerja (Sen–Jum, 08:00–17:00):** Meneruskan permintaan ke loket operasional utama.
* **Luar Jam (Sen–Jum, 17:01–07:59):** Meneruskan permintaan ke loket utama dan tim siaga darurat 24/7 (serta menyalin/CC pengirim asli).
* **Akhir Pekan (Sabtu–Minggu):** Meneruskan permintaan langsung ke tim siaga akhir pekan.


* **Retensi Lampiran:** Mengambil ulang payload email secara penuh beserta seluruh lampirannya untuk diteruskan ke penerima akhir tanpa kehilangan data.

### 🛠️ Tech Stack & Komponen

* **Platform:** Microsoft Power Automate
* **Konektor:** Office 365 Outlook API
* **Logika Komputasi:** Konversi zona waktu berbasis expression, evaluasi hari (Day-of-Week), dan logika percabangan kondisional.

### 🚀 Panduan Penggunaan & Konfigurasi

Repositori ini memuat konfigurasi flow yang harus disesuaikan sebelum dijalankan di environment produksi.

**1. Deployment Paket**

* Buka portal Power Automate, akses menu **Solutions** atau **My flows**, lalu pilih **Import**.
* Unggah paket rilis yang memuat file manifest dan flow definition.
* Pada bagian pemetaan koneksi, arahkan konektor **Office 365 Outlook** ke *service account* yang memiliki otorisasi penuh pada kotak masuk operasional. Jangan gunakan akun pribadi.

**2. Konfigurasi Parameter Inti**
Buka mode Edit pada flow dan ubah nilai pada node berikut sesuai kebutuhan environment Anda:

* **Cutover Timestamp:** Pada node gate go-live, masukkan tanggal dan waktu mulai beroperasinya sistem dengan format `YYYY-MM-DDTHH:mm:ss`.
* **Zona Waktu:** Jika operasi tidak menggunakan `SE Asia Standard Time`, ubah nilai pada action `Convert time zone` dan node Compose waktu lokal.
* **Filter Pengirim:** Sesuaikan daftar email eksak dan keyword/domain parsial yang harus diabaikan sistem untuk meminimalisir sampah (*noise*).
* **Domain Partner:** Masukkan domain partner/agensi tujuan untuk mencegah *loop* dari balasan otomatis mereka.
* **Alamat Kotak Masuk:** Masukkan alamat email operasional Anda pada node validasi tujuan agar email BCC/nyasar tidak diproses.
* **Target Penerima:** Masukkan alamat email asli untuk loket standar dan tim siaga 24 jam pada setiap node *Reply to email*.

**3. Mode Pengujian (Test Mode)**
Simulasikan waktu tanpa harus menunggu jam aktual dengan mengubah expression di node Compose:

* **DayNumber:** Ganti expression dengan angka statis. 0 untuk Minggu hingga 6 untuk Sabtu.
* **CurrentHour:** Ganti expression dengan angka statis dari 0 hingga 23.
* *Sangat Penting:* Gunakan alamat email testing Anda sendiri sebagai target penerima agar email tidak terkirim ke partner operasional selama uji coba.
* *Pembersihan:* Kembalikan parameter `DayNumber` dan `CurrentHour` ke formula pembacaan waktu aslinya setelah pengujian selesai.

**4. Checklist Pre-Production**
Validasi poin-poin berikut sebelum mengaktifkan flow untuk memproses email sungguhan:

* [ ] Koneksi sudah menggunakan *service account*.
* [ ] Tanggal cutover go-live terisi valid.
* [ ] Seluruh email pengujian sudah diganti dengan alamat tujuan yang sebenarnya.
* [ ] Angka statis pada `DayNumber` dan `CurrentHour` sudah dihapus dan dikembalikan ke formula dinamis.
* [ ] Fitur langganan peringatan kegagalan (*Failure alert subscription*) diarahkan ke akun *maintainer* sistem.
