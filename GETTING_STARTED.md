# 🚀 Getting Started - Login & Authentication System

## 📌 Overview

Sistem login dan database telah berhasil ditambahkan ke project **Plantation Manager**. Berikut adalah panduan lengkap untuk menjalankan dan menggunakan fitur authentication ini.

---

## ⚡ Quick Start (5 Menit)

### Step 1: Install Dependencies

```bash
npm install
```

### Step 2: Setup Environment

Buat file `.env.local` di root project:

```bash
# .env.local
NEXT_PUBLIC_API_URL=http://localhost:3000
JWT_SECRET=your-super-secret-key-change-in-production
JWT_EXPIRATION=7d
```

> ⚠️ **PENTING**: Ganti `JWT_SECRET` dengan nilai yang lebih panjang dan kompleks untuk production!

### Step 3: Run Development Server

```bash
npm run dev
```

Server akan berjalan di **http://localhost:3000**

### Step 4: Test Authentication

#### Register User Baru

1. Buka: http://localhost:3000/register
2. Isi form dengan data:
   - Nama Lengkap: Budi Santoso
   - Username: budisantoso
   - Email: budi@example.com
   - Password: password123
   - Konfirmasi: password123
3. Klik "Daftar"

#### Login

1. Buka: http://localhost:3000/login
2. Masukkan:
   - Username/Email: budisantoso atau budi@example.com
   - Password: password123
3. Klik "Masuk"

#### Dashboard

Setelah login berhasil, Anda akan diarahkan ke: http://localhost:3000/dashboard

Di sini Anda bisa:

- Melihat informasi user yang login
- Melihat token yang disimpan (di browser dev tools → localStorage)
- Klik "Logout" untuk keluar

---

## 📂 File Structure

### Tabel Database Users

```sql
CREATE TABLE users (
  id TEXT PRIMARY KEY,           -- Unique ID
  username TEXT UNIQUE NOT NULL, -- For login
  email TEXT UNIQUE NOT NULL,    -- For login & verification
  password TEXT NOT NULL,        -- Hashed with bcryptjs
  fullName TEXT,                 -- User's full name
  createdAt TEXT NOT NULL        -- Account creation time
)
```

### New Routes

- `GET /login` - Login page
- `GET /register` - Register page
- `GET /dashboard` - Protected dashboard page
- `POST /api/auth/login` - Login API
- `POST /api/auth/register` - Register API

### New Components

- `LoginForm` - Login form component
- `RegisterForm` - Register form component

### New Utilities

- `useAuth()` - React hook for authentication
- `verifyToken()` - JWT verification function

---

## 📚 Documentation Files

| File                   | Deskripsi                               |
| ---------------------- | --------------------------------------- |
| **SETUP_LOGIN.md**     | Dokumentasi lengkap setup & usage       |
| **API_TESTING.md**     | Panduan testing API dengan examples     |
| **FLOW_DIAGRAM.md**    | Diagram alur authentication & data flow |
| **SUMMARY.md**         | Ringkasan implementasi                  |
| **GETTING_STARTED.md** | File ini - panduan quick start          |

---

## 🔐 Features

✅ User registration dengan validasi  
✅ Secure login dengan JWT token  
✅ Password hashing dengan bcryptjs  
✅ Protected routes untuk authenticated users  
✅ User logout functionality  
✅ Token storage di localStorage  
✅ Responsive design dengan Tailwind CSS  
✅ Error handling yang baik

---

## 🛠️ API Endpoints

### Register

```
POST /api/auth/register
Content-Type: application/json

{
  "username": "string",
  "email": "string@example.com",
  "password": "string",
  "fullName": "string"
}

Response (201):
{
  "message": "User berhasil didaftarkan",
  "userId": "uuid"
}
```

### Login

```
POST /api/auth/login
Content-Type: application/json

{
  "username": "string",
  "password": "string"
}

Response (200):
{
  "message": "Login berhasil",
  "token": "jwt-token-string",
  "user": {
    "id": "uuid",
    "username": "string",
    "email": "string@example.com",
    "fullName": "string"
  }
}
```

---

## 💡 Usage Examples

### Testing dengan cURL

#### Register

```bash
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "email": "test@example.com",
    "password": "password123",
    "fullName": "Test User"
  }'
```

#### Login

```bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "password123"
  }'
```

### Menggunakan useAuth Hook

```typescript
'use client';

import { useAuth } from '@/lib/useAuth';

export default function MyComponent() {
  const { user, isAuthenticated, logout } = useAuth();

  if (!isAuthenticated) {
    return <p>Please login first</p>;
  }

  return (
    <div>
      <h1>Welcome, {user?.fullName}</h1>
      <p>Email: {user?.email}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

---

## 🔍 Troubleshooting

### Error: "Cannot find module 'bcryptjs'"

**Solusi**: Pastikan dependencies sudah terinstall

```bash
npm install
```

### Error: "JWT_SECRET is undefined"

**Solusi**: Buat file `.env.local` dengan JWT_SECRET:

```
JWT_SECRET=your-secret-key
```

### Error: "UNIQUE constraint failed: users.username"

**Solusi**: Username sudah digunakan, gunakan username yang berbeda

### Error: Database file tidak ditemukan

**Solusi**: Database akan auto-create saat aplikasi pertama kali dijalankan. Pastikan folder project memiliki write permission.

### Tidak bisa akses dashboard setelah login

**Solusi**:

- Pastikan token tersimpan di localStorage (cek di DevTools)
- Refresh page
- Coba clear localStorage dan login ulang

---

## 🔒 Security Best Practices

### Development

✅ JWT_SECRET bebas diatur untuk testing

### Production

❌ JANGAN gunakan JWT_SECRET yang mudah ditebak
✅ Gunakan nilai random yang panjang (min 32 karakter)
✅ Gunakan HTTPS bukan HTTP
✅ Implementasikan rate limiting
✅ Tambahkan input validation yang ketat
✅ Implementasikan CSRF protection
✅ Tambahkan email verification
✅ Implementasikan refresh token mechanism

---

## 🧪 Testing Checklist

- [ ] Registrasi user baru berhasil
- [ ] Tidak bisa register dengan username/email yang sama
- [ ] Login berhasil dengan credentials yang benar
- [ ] Token disimpan di localStorage
- [ ] Dashboard bisa diakses setelah login
- [ ] Tidak bisa akses dashboard tanpa login (redirect ke /login)
- [ ] Logout menghapus token dari localStorage
- [ ] Password ter-hash di database (bukan plain text)
- [ ] Form validation berfungsi (required fields, password min length)
- [ ] Error messages tampil dengan benar

---

## 📦 Dependencies yang Ditambahkan

```json
{
  "dependencies": {
    "bcryptjs": "^2.4.3", // Password hashing
    "jsonwebtoken": "^9.1.2" // JWT token generation
  },
  "devDependencies": {
    "@types/bcryptjs": "^2.4.6", // TypeScript types
    "@types/jsonwebtoken": "^9.0.7" // TypeScript types
  }
}
```

---

## 🎯 Next Steps

Setelah berhasil setup login, pertimbangkan untuk:

### Phase 2: Integration

- [ ] Link login dengan existing features (Budget, Plantation)
- [ ] Tambahkan userId ke setiap transaction
- [ ] Filter data berdasarkan user yang login
- [ ] Tampilkan user info di navbar/header

### Phase 3: Enhancement

- [ ] Email verification saat registrasi
- [ ] Password reset functionality
- [ ] User profile edit page
- [ ] Change password feature
- [ ] Remember me functionality

### Phase 4: Production Ready

- [ ] Implement refresh tokens
- [ ] Add rate limiting
- [ ] Add CSRF protection
- [ ] Comprehensive input validation
- [ ] Audit logging
- [ ] Two-factor authentication (2FA)

---

## 📞 Support

Untuk pertanyaan atau issue:

1. Baca documentation files (SETUP_LOGIN.md, API_TESTING.md)
2. Periksa browser console untuk error messages
3. Lihat server logs di terminal
4. Cek database dengan SQLite viewer

---

## 📄 License

Project ini adalah bagian dari Plantation Manager System.

---

## ✨ Summary

Sistem login dan authentication sudah siap digunakan!

- ✅ Database setup lengkap
- ✅ API endpoints siap
- ✅ UI components tersedia
- ✅ Security measures implemented
- ✅ Documentation lengkap

**Silakan jalankan `npm install && npm run dev` dan mulai testing!**

Happy coding! 🌾🚀
