# Prompt Library

Aplikasi web single-file untuk menyimpan, mengelola, dan menggunakan kembali prompt AI (ChatGPT, Claude, Gemini, Midjourney, dll) — lengkap dengan kategori, koleksi, variabel dinamis, riwayat versi, dan pencarian.

Dibangun sebagai **single HTML file** (HTML + CSS + JavaScript, tanpa framework, tanpa proses build) sehingga bisa langsung dibuka di browser atau di-host di GitHub Pages.

## ✨ Fitur

- **Grid & list view** dengan pagination
- **Search & filter** berdasarkan model AI, kategori, status, dan urutan
- **Kategori & koleksi** kustom (tambah/hapus)
- **Detail panel** per prompt:
  - Tab **Prompt** — isi prompt, jumlah kata/karakter, estimasi token, badge kompleksitas
  - Tab **Preview** — hasil prompt setelah variabel `{{VARIABEL}}` disubstitusi
  - Tab **Versions** — riwayat perubahan & pulihkan versi lama
  - Tab **Notes** — catatan pribadi per prompt
- **Auto-detect variabel** `{{NAMA_VARIABEL}}` dari teks prompt, dengan nilai default yang bisa disimpan
- **Copy ke clipboard** dengan variabel yang sudah tersubstitusi
- **Favorite**, **trash/restore**, dan hapus permanen
- **Dark mode** dengan preferensi tersimpan otomatis
- **Responsive** — sidebar hamburger untuk tampilan mobile
- **Ekspor data** ke JSON dan reset ke data contoh (lewat halaman Pengaturan)
- **Login dengan Google & sinkronisasi cloud** — data tersimpan di Firebase Firestore per akun, realtime, bisa diakses dari perangkat manapun
- **PWA (Progressive Web App)** — bisa di-install ke home screen (desktop/mobile) dan tetap bisa dibuka saat offline berkat `manifest.json` + `service-worker.js`

## ☁️ Setup Firebase (wajib sebelum dipakai online)

Aplikasi ini menggunakan **Firebase Authentication (Google Sign-In)** dan **Cloud Firestore** sebagai database. Konfigurasi project sudah tertanam di `index.html`, tapi kamu tetap perlu mengaktifkan 2 hal ini di [Firebase Console](https://console.firebase.google.com/) pada project `prompt-library-b1f90`:

**1. Aktifkan Google Sign-In**
- Buka **Authentication → Sign-in method**.
- Aktifkan provider **Google**.
- Di **Authentication → Settings → Authorized domains**, tambahkan domain GitHub Pages kamu, misalnya `<username>.github.io`.

**2. Buat Firestore Database + Security Rules**
- Buka **Firestore Database → Create database** (mode production).
- Di tab **Rules**, ganti dengan aturan berikut supaya setiap user cuma bisa baca/tulis data miliknya sendiri:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /promptLibraryUsers/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

Setelah itu, buka situsnya → klik **"Masuk dengan Google"** → data otomatis tersimpan/tersinkron di dokumen `promptLibraryUsers/{uid}`.

> ⚠️ **Catatan keamanan:** `apiKey` Firebase yang tertanam di `index.html` **aman untuk publik** — ini bukan secret, keamanan data diatur lewat Firestore Security Rules di atas, bukan dengan menyembunyikan config. Jangan lupa tetap pasang rules-nya supaya orang lain tidak bisa membaca/menulis data user lain.

## 🚀 Cara Pakai

### Langsung di browser
Cukup buka `index.html` di browser mana pun. Tidak ada instalasi atau dependency.

### Deploy ke GitHub Pages
1. Push repository ini ke GitHub.
2. Buka **Settings → Pages**.
3. Pilih source: branch `main`, folder `/ (root)`.
4. Simpan — situs akan tersedia di `https://<username>.github.io/<nama-repo>/`.

## 📁 Struktur File

```
├── index.html          # Aplikasi utama (HTML + CSS + JS, single file)
├── manifest.json        # Web app manifest (PWA)
├── service-worker.js    # Caching offline (PWA)
├── icons/
│   ├── icon-192.png
│   └── icon-512.png
├── README.md
├── LICENSE
└── .gitignore
```

> Catatan: `service-worker.js` hanya aktif kalau situs diakses lewat HTTPS (termasuk GitHub Pages) atau `localhost`. Membuka `index.html` langsung dari file explorer (`file://`) tidak akan mengaktifkan fitur offline-nya, tapi aplikasi tetap berfungsi normal.

## 📲 Install sebagai App (PWA)

Setelah di-deploy (misalnya ke GitHub Pages), buka situsnya lalu:
- **Desktop (Chrome/Edge):** klik ikon install di address bar, atau menu → "Install Prompt Library".
- **Android (Chrome):** menu (⋮) → "Add to Home screen".
- **iOS (Safari):** tombol Share → "Add to Home Screen".

## 🗂️ Struktur Data

Data disimpan sebagai satu dokumen Firestore per user di path `promptLibraryUsers/{uid}`, berisi:

```json
{
  "prompts": [ /* daftar prompt */ ],
  "categories": [ /* daftar kategori */ ],
  "collections": [ /* daftar koleksi */ ],
  "updatedAt": 1234567890
}
```

Perubahan tersinkron **realtime** lewat Firestore `onSnapshot` — kalau kamu buka di 2 tab/perangkat sekaligus dengan akun yang sama, perubahan di satu tempat langsung muncul di tempat lain.

Preferensi tema (light/dark) tetap disimpan lokal per perangkat di `localStorage` (key `promptLibrary.theme`), karena ini preferensi tampilan, bukan data yang perlu disinkronkan.

> Gunakan tombol **Ekspor JSON** di halaman Pengaturan untuk mencadangkan data secara berkala di luar Firestore.

## 🛠️ Tech Stack

- HTML5, CSS3 (CSS variables untuk light/dark theme), Vanilla JavaScript (ES6+)
- Tanpa dependency eksternal, tanpa build step

## 📄 Lisensi

Dirilis di bawah [MIT License](LICENSE) — bebas digunakan dan dimodifikasi.
