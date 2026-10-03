# TM-2: Kartu HTTP Method + Code

## 1. Kategori 200 (OK)
- **Endpoint:** `GET /api/v1`
- **Kode & Status:** 200 OK[cite: 2]
- **Message:** Server mengembalikan halaman HTML dengan judul "SILAB berpindah ke ATLAS | UNIRA" dan pesan "SILAB telah berpindah ke ATLAS"[cite: 2].
- **Siapa salah + Aksi perbaikan:** Tidak ada yang salah. Klien berhasil melakukan *request*, dan server membalas dengan dokumen HTML yang berisi informasi pengalihan rute ke domain baru (ATLAS).

## 2. Kategori 401 (Kredensial / Akses)
- **Endpoint:** `GET /api/v1/jadwal/jumlah-jadwal`[cite: 2]
- **Kode & Status:** 401 Unauthorized[cite: 2]
- **Message:** `"Akses tidak diizinkan!"`[cite: 2]
- **Siapa salah + Aksi perbaikan:** Klien salah karena mencoba mengakses data jadwal tanpa memiliki hak otorisasi. Aksi perbaikan: Klien harus menyediakan kredensial yang valid (seperti Bearer Token pada *header*) sebelum melakukan *request* ke *endpoint* ini.

## 3. Kategori 401 (Seharusnya untuk memancing 400 Bad Request)
- **Endpoint:** `POST /api/v1/jadwal`[cite: 2]
- **Kode & Status:** 401 Unauthorized[cite: 2]
- **Message:** `"Akses tidak diizinkan!"`[cite: 2]
- **Siapa salah + Aksi perbaikan:** Saat ini, *request* dicegat oleh sistem keamanan server (menghasilkan 401) sebelum bisa memvalidasi isi *body*[cite: 2]. Aksi perbaikan: Klien harus menyertakan token otorisasi yang valid terlebih dahulu agar bisa menembus penjagaan awal. Setelah akses diizinkan, barulah klien harus mengirim *body request* yang lengkap agar tidak terkena *error* 400 Bad Request.

## 4. Kategori 404 (Not Found)
- **Endpoint:** `GET /api/v1/xxx`[cite: 2]
- **Kode & Status:** 404 Not Found[cite: 2]
- **Message:** `"Api atau berkas tidak ditemukan"`[cite: 2]
- **Siapa salah + Aksi perbaikan:** Klien salah karena mengakses *path* (`/xxx`) yang tidak terdaftar di sistem *router* *backend*[cite: 2]. Aksi perbaikan: Klien harus memperbaiki URL *request* dan memastikannya sesuai dengan dokumentasi API yang tersedia.

## 5. Kategori GraphQL Response
- **Endpoint:** `POST https://api.unira.ac.id/graphql`[cite: 2]
- **Kode & Status:** 200 OK[cite: 2]
- **Message:** Mengembalikan data JSON yang berisi `"hello": "UNIRA GRAPHQL API"`[cite: 2].
- **Siapa salah + Aksi perbaikan:** Tidak ada yang salah. Ini membuktikan bahwa klien berhasil terhubung ke server GraphQL. Jika ke depannya terjadi *error* pada saat memanggil data spesifik, GraphQL akan tetap mengembalikan status 200 OK, dan *developer* harus membaca detail *error*-nya di dalam *body response*.

---
**Lampiran:** File hasil export Postman disimpan di `docs/assets/tm2-postman-export.json`