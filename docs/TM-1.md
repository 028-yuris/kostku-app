# TM-1: Peta Routing Pribadi 

**Nama:** Yuris Ikrar Rabbani  
**NPM:** 2024520028  
**Domain Entitas:** Kamar (Aplikasi Kostku)

Pemetaan URL kontrak 3 lapis (API, Frontend Web, dan Mobile Flutter) untuk entitas **Kamar**.

| User Story | METHOD + /api/v1/... | SvelteKit Route | ATLAS SPA Route | Flutter Route & Args | Authorize / canAccess |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **US-01:** Menampilkan daftar katalog kamar | `GET /api/v1/kamar` | `routes/kamar/+page.svelte` | `/kamar` | `KamarListScreen()` <br>*(no args)* | `authorize('Pemilik', 'Pencari')` |
| **US-02:** Menampilkan form tambah kamar baru | `POST /api/v1/kamar` | `routes/kamar/baru/+page.svelte` | `/kamar/baru` | `KamarFormScreen(isEdit: false)` <br>*(no args)* | `authorize('Pemilik')` |
| **US-03:** Melihat detail spesifik satu kamar | `GET /api/v1/kamar/:id` | `routes/kamar/[id]/+page.svelte` | `/kamar/:id` | `KamarDetailScreen(id: String)` | `authorize('Pemilik', 'Pencari')` |
| **US-04:** Mengedit data kamar miliknya | `PUT /api/v1/kamar/:id` | `routes/kamar/[id]/edit/+page.svelte` | `/kamar/:id/edit` | `KamarFormScreen(isEdit: true, id: String)` | `authorize('Pemilik')` <br> `canAccess(kamar.pemilik_id === user.id)` |
| **US-05:** Menghapus data kamar miliknya | `DELETE /api/v1/kamar/:id` | *Form Action* di `routes/kamar/[id]/edit/+page.svelte` | *Action on* `/kamar/:id/edit` | `showDeleteConfirm(id: String)` | `authorize('Pemilik')` <br> `canAccess(kamar.pemilik_id === user.id)` |

## Pemenuhan Kriteria Soal:
1. **Prefix /api/v1 benar:** Semua *endpoint* API menggunakan awalan standar.
2. **[id] vs baru tidak tertukar:** *Route* statis `baru` dieksekusi lebih dulu oleh *router* (dipisah foldernya) sehingga tidak ditangkap secara keliru oleh parameter dinamis `[id]`.
3. **Authorize/canAccess:** Pengecekan otorisasi dibedakan berdasarkan *Role*. Pada aksi `PUT` dan `DELETE`, terdapat fungsi `canAccess()` untuk memastikan Pemilik hanya bisa memanipulasi data kamar miliknya sendiri.