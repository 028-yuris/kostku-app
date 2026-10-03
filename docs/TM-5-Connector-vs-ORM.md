# TM-5: Connector vs ORM

## A. Raw mysql2 (Pendekatan Tradisional)
Berikut adalah contoh implementasi fungsi *create* dan *count* menggunakan *driver* bawaan `mysql2`.

```javascript
// 1. totalJadwal (Raw SQL)
const [rows] = await pool.query('SELECT COUNT(*) AS total FROM jadwal');
const total = rows[0].total;

// 2. create.js (Raw SQL)
const { mata_kuliah, hari, id_dosen } = req.body;
// Contoh penulisan query menggunakan Parameterized Query untuk keamanan
const [result] = await pool.query(
  'INSERT INTO jadwal (mata_kuliah, hari, id_dosen) VALUES (?, ?, ?)',
  [mata_kuliah, hari, id_dosen]
);
```

## B. Prisma (Pendekatan ORM)
Berikut adalah penulisan ulang logika yang sama, namun menggunakan Prisma ORM.

```javascript
// 1. totalJadwal (Prisma)
const total = await prisma.jadwal.count();

// 2. create.js (Prisma)
const { mata_kuliah, hari, id_dosen } = req.body;
const jadwalBaru = await prisma.jadwal.create({
  data: {
    mata_kuliah: mata_kuliah,
    hari: hari,
    id_dosen: id_dosen
  }
});
```

---

## Analisis Keamanan

**Risiko SQL Injection pada Raw SQL:**
Risiko paling fatal saat menulis SQL mentah adalah ketika *developer* menggabungkan string input dari *user* langsung ke dalam perintah SQL (contoh: `` pool.query(`SELECT * FROM jadwal WHERE id = ${req.body.id}`) ``). Jika penyerang memasukkan *payload* seperti `1 OR 1=1`, seluruh data tabel bisa terekspos karena *server* menganggap input tersebut sebagai bagian dari perintah yang sah.

**Bagaimana Sequelize/Prisma Menutup Celah Tersebut:**
ORM seperti Prisma atau Sequelize secara bawaan ( *by default* ) membungkus semua operasi ke dalam ***Parameterized Queries*** (atau *Prepared Statements*). Artinya, struktur logika eksekusi SQL dipisahkan sepenuhnya dari data/input yang dikirimkan. Ketika *user* mengirimkan `1 OR 1=1`, ORM akan memastikan *database* membaca itu murni sebagai sebuah teks atau *value* biasa, bukan sebagai perintah eksekusi SQL yang valid.