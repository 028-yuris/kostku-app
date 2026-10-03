# TM-3: Perbandingan 4 Paradigma API

**Studi Kasus:** Halaman *dashboard* membutuhkan tiga data sekaligus: Total Jadwal, Daftar Jadwal, dan Tagihan Mahasiswa.

## 1. REST API (N-Request)
Dalam arsitektur REST, *client* harus melakukan *multiple requests* (N-request) secara terpisah ke *endpoint* yang berbeda karena setiap *endpoint* mewakili satu *resource* spesifik.
- `GET /api/v1/jadwal/jumlah-jadwal` (mengambil total jadwal)
- `GET /api/v1/jadwal` (mengambil array daftar jadwal)
- `GET /api/v1/tagihan` (mengambil data tagihan)

**Karakteristik:** Rawan terjadi *under-fetching* (harus memanggil banyak API untuk satu tampilan) dan *over-fetching* (data balasan terlalu besar/mengandung *field* yang tidak dibutuhkan UI). Membebani *network bandwidth* karena banyaknya *round-trip* HTTP.

## 2. GraphQL (1 Request)
GraphQL menyelesaikan masalah REST dengan mengizinkan *client* meminta berbagai entitas data secara spesifik hanya dalam 1 *request* melalui satu *endpoint* (`POST /graphql`).

```graphql
query getDashboardData {
  dashboardTrend {
    totalJadwal
    daftarJadwal {
      id
      mata_kuliah
      hari
    }
    tagihan {
      status
      jumlah
    }
  }
}
```

**Karakteristik:** Sangat efisien. Server hanya merespons dengan struktur JSON persis seperti yang diminta (tidak ada *over-fetching*). Sangat ideal untuk halaman *dashboard* yang butuh agregasi data.

## 3. gRPC (Hipotetik Antar-Service)
Jika arsitektur *backend* menggunakan *microservices* (misal: Service Jadwal dan Service Keuangan terpisah), gRPC digunakan untuk komunikasi antar-server tersebut.

**Karakteristik:** Menggunakan HTTP/2 dan *protocol buffers* (Protobuf). Format pesannya berupa biner sehingga sangat cepat dan ringan. Namun, paradigma ini umumnya dipakai untuk komunikasi *backend-to-backend*, bukan dipanggil langsung oleh *browser* (kecuali menggunakan perantara gRPC-Web).

## 4. WebSocket / Polling (Live Refresh)
Jika *dashboard* memiliki kebutuhan bisnis bahwa angka total jadwal atau tagihan harus *update* secara langsung (misal saat masa pengisian KRS massal).

**Karakteristik:** 
- **Polling:** *Client* menembak REST API setiap beberapa detik (sangat membebani *server*).
- **WebSocket:** Menciptakan koneksi dua arah yang terus terbuka (persisten). *Server* bisa langsung mendorong (*push*) data terbaru ke *dashboard client* tanpa perlu *client* melakukan *request* berulang-ulang.

---

## Kesimpulan

**Pemenang: GraphQL**

**Alasan:** Untuk kasus *dashboard* yang butuh merakit banyak data (agregasi) dari berbagai domain/tabel, GraphQL adalah pilihan paling optimal. *Frontend* cukup melakukan 1 kali *request*, menghemat *bandwidth*, dan secara drastis mempercepat waktu muat halaman (*Time to View*) dibandingkan harus menunggu 3 *request* REST API selesai.

**Kapan akan ganti pilihan:**
Saya akan mengganti arsitektur kembali ke **REST API** jika *dashboard* ini bersifat publik (tidak perlu login) dan trafiknya sangat tinggi, karena saya membutuhkan *HTTP Caching* level jaringan (CDN) yang mudah dikonfigurasi pada REST (menggunakan GET), sedangkan GraphQL (menggunakan POST) lebih rumit untuk di-cache. Saya akan beralih ke **WebSocket** jika *Product Manager* mewajibkan metrik di *dashboard* bergerak secara *real-time* (seperti *dashboard* saham).