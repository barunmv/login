# Uji API

Sebelum menguji, jalankan migrasi Supabase sesuai [README](./README.md), atur
`.env.local`, lalu jalankan `npm run dev`. Gunakan password minimal 8 karakter.

## Daftar akun

```powershell
curl.exe -X POST http://localhost:3000/api/auth/register `
  -H "Content-Type: application/json" `
  -d "{\"username\":\"testuser\",\"email\":\"test@example.com\",\"password\":\"password-aman-123\",\"fullName\":\"Test User\"}"
```

## Login

```powershell
curl.exe -X POST http://localhost:3000/api/auth/login `
  -H "Content-Type: application/json" `
  -d "{\"username\":\"testuser\",\"password\":\"password-aman-123\"}"
```

Salin `token` dari respons login untuk menguji endpoint privat:

```powershell
curl.exe http://localhost:3000/api/plantation `
  -H "Authorization: Bearer <TOKEN>"
```

`GET /api/health` menguji koneksi server ke Supabase. Endpoint daftar akun dan
login memiliki batas percobaan; saat menguji, gunakan alamat IP dan kredensial
yang sesuai. Jangan masukkan token atau kunci Supabase ke repositori.
