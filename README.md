# Kelola Kebun

Aplikasi pencatatan kebun, transaksi, dan laporan perkembangan tanaman. Dibangun dengan Next.js, TypeScript, Supabase PostgreSQL, dan Supabase Storage.

Memerlukan Node.js 20.19 atau lebih baru.

## Fitur

- Catat kebun, jenis tanaman, luas lahan, tanggal tanam, dan perkiraan panen.
- Buat laporan perkembangan dengan catatan serta foto dari galeri atau kamera ponsel.
- Pantau status kebun, riwayat laporan, transaksi, dan ringkasan anggaran.
- Data tiap akun dipisahkan berdasarkan pemiliknya.

## Menyiapkan Supabase

1. Buat proyek di [Supabase](https://supabase.com/dashboard).
2. Buka **SQL Editor**, lalu jalankan isi [`supabase/migrations/20261104000000_initial_schema.sql`](./supabase/migrations/20261104000000_initial_schema.sql).
   Skrip membuat tabel, indeks, mengaktifkan RLS, dan membuat bucket privat `progress-photos`.
3. Dari **Project Settings → API**, salin Project URL dan `service_role` key. Jangan pernah membagikan key ini atau memasukkannya ke variabel `NEXT_PUBLIC_*`.
4. Salin `.env.example` menjadi `.env.local`, lalu isi `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, dan `JWT_SECRET`. Buat JWT secret acak minimal 32 karakter; gunakan nilai berbeda untuk setiap lingkungan.
5. Instal dependensi dan jalankan aplikasi:

   ```powershell
   npm install
   npm run dev
   ```

Kredensial Supabase hanya digunakan pada API server. Endpoint data meminta JWT akun dan memfilter setiap operasi dengan `user_id`. Tabel dan bucket bersifat privat; key service role tidak dikirim ke browser.

## Pengembangan dan rilis

```powershell
npm run typecheck
npm run lint
npm run build
npm start
```

Untuk rilis di Vercel atau host Node.js, hubungkan repositori lalu tambahkan variabel lingkungan yang sama (`SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `JWT_SECRET`, `JWT_EXPIRATION`) pada konfigurasi Production dan Preview. Jangan unggah `.env.local`. Setelah variabel diatur, lakukan deployment ulang dan uji daftar akun, login, pencatatan transaksi, data kebun, serta unggah foto.

Login dan pendaftaran dibatasi berdasarkan alamat IP menggunakan tabel Supabase. Terapkan aplikasi di belakang proxy tepercaya yang mengisi `x-real-ip` atau `x-forwarded-for`; jangan menerima header tersebut langsung dari klien internet tanpa proxy tepercaya. Atur backup database di Supabase dan uji pemulihannya. Supabase Storage menyimpan foto secara privat dan API memberikan signed URL sementara.

Setelah deployment, buka `/api/health`; respons `200` menunjukkan API dapat menjangkau tabel Supabase.

## Data SQLite lama

Data lokal di `plantation.db` **tidak otomatis dipindahkan** ke Supabase. Buat backup sebelum migrasi. Data lama belum memiliki pemilik untuk setiap transaksi dan kebun, sehingga perlu dipetakan ke akun yang tepat sebelum diimpor; jangan mengimpor semua data bersama-sama tanpa menentukan pemiliknya.

## Struktur

- `src/app/api`: endpoint server untuk autentikasi dan data.
- `src/lib/supabase.ts`: koneksi server-only ke Supabase.
- `src/lib/api-auth.ts`: validasi JWT untuk endpoint privat.
- `src/lib/progress-photo.ts`: validasi dan penyimpanan foto di bucket privat.
- `supabase/migrations`: skema dan konfigurasi awal Supabase.
