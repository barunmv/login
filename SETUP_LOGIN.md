# Menyiapkan akun dan Supabase

Login serta pendaftaran menggunakan API server dan tabel Supabase `users`.
Ikuti bagian **Menyiapkan Supabase** di [README](./README.md) untuk membuat
skema dan mengatur variabel lingkungan.

Password di-hash dengan bcrypt sebelum disimpan. Login menghasilkan JWT yang
dipakai API untuk membatasi data kebun, transaksi, dan laporan foto per akun.
Setiap API data memerlukan header `Authorization: Bearer <token>`.
