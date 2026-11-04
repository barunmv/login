# 🔄 Authentication Flow Diagram

## Registration Flow

```
User (Browser)
    ↓
[/register page]
    ↓
[RegisterForm Component]
    ↓
[User fills form]
    ↓
[Click "Daftar" button]
    ↓
POST /api/auth/register {username, email, password, fullName}
    ↓
[Server - register/route.ts]
    ↓
[Validate input]
    ↓
[Hash password with bcryptjs]
    ↓
[Generate unique UUID]
    ↓
[Insert into database users table]
    ↓
[Success Response] ← OR → [Error Response]
    ↓                      ↓
[Show success msg]   [Show error msg]
    ↓
[Auto redirect to /login after 2s]
```

---

## Login Flow

```
User (Browser)
    ↓
[/login page]
    ↓
[LoginForm Component]
    ↓
[User fills username & password]
    ↓
[Click "Masuk" button]
    ↓
POST /api/auth/login {username, password}
    ↓
[Server - login/route.ts]
    ↓
[Query database for user]
    ↓
User Found?
    ├─ NO → [Return 401: User tidak ditemukan]
    │           ↓
    │       [Show error message]
    │
    └─ YES
        ↓
    [Compare password with bcryptjs]
        ↓
    Password match?
        ├─ NO → [Return 401: Password salah]
        │           ↓
        │       [Show error message]
        │
        └─ YES
            ↓
        [Generate JWT token]
            ↓
        [Return token + user data]
            ↓
        [Client saves token to localStorage]
            ↓
        [Redirect to /dashboard]
```

---

## Dashboard Access Flow

```
User accesses /dashboard
    ↓
[Dashboard page loads]
    ↓
[useAuth() hook checks localStorage]
    ↓
Token exists?
    ├─ NO
    │   ↓
    │   [Show "Memuat..." message]
    │   ↓
    │   [Redirect to /login]
    │   ↓
    │   [User cannot access dashboard]
    │
    └─ YES
        ↓
        [Parse token from localStorage]
        ↓
        [Display user information]
        ↓
        [Show dashboard content]
        ↓
        [User can click "Logout"]
            ↓
        [Remove token from localStorage]
            ↓
        [Clear user state]
            ↓
        [Redirect to /login]
```

---

## Protected Route Logic

```
useAuth() Hook
    ↓
┌───────────────────────────────────┐
│ Check localStorage for:           │
│ - token                           │
│ - user (JSON string)              │
└───────────────────────────────────┘
    ↓
Return Object:
{
  user: User | null
  token: string | null
  isLoading: boolean
  isAuthenticated: boolean
  logout: () => void
}
    ↓
Use in components:
- if (!isAuthenticated) → redirect to /login
- Display user info
- Call logout() function
```

---

## Database Schema

```
TABLE: users
┌─────────────────┬──────────┬────────┬──────────────┐
│ id              │ PRIMARY  │ TEXT   │ UUID format  │
├─────────────────┼──────────┼────────┼──────────────┤
│ username        │ UNIQUE   │ TEXT   │ Required     │
├─────────────────┼──────────┼────────┼──────────────┤
│ email           │ UNIQUE   │ TEXT   │ Required     │
├─────────────────┼──────────┼────────┼──────────────┤
│ password        │ -        │ TEXT   │ Hashed       │
├─────────────────┼──────────┼────────┼──────────────┤
│ fullName        │ -        │ TEXT   │ Optional     │
├─────────────────┼──────────┼────────┼──────────────┤
│ createdAt       │ -        │ TEXT   │ ISO datetime │
└─────────────────┴──────────┴────────┴──────────────┘
```

---

## Data Flow During Login

```
Request Body:
{
  "username": "johndoe",
  "password": "password123"
}
    ↓
[1. Search database]
    ↓
User Record:
{
  id: "550e8400-e29b-41d4-a716-446655440000",
  username: "johndoe",
  email: "john@example.com",
  password: "$2a$10$...", // hashed
  fullName: "John Doe",
  createdAt: "2024-01-15T10:30:00Z"
}
    ↓
[2. Verify password]
    ↓
bcryptjs.compare("password123", "$2a$10$...") → true
    ↓
[3. Generate JWT]
    ↓
JWT Token Created:
{
  header: {
    alg: "HS256",
    typ: "JWT"
  },
  payload: {
    userId: "550e8400-e29b-41d4-a716-446655440000",
    username: "johndoe",
    email: "john@example.com",
    iat: 1705316400,
    exp: 1705921200  // +7 days
  },
  signature: "..."
}
    ↓
[4. Response to client]
    ↓
Response Body:
{
  "message": "Login berhasil",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "username": "johndoe",
    "email": "john@example.com",
    "fullName": "John Doe"
  }
}
    ↓
[5. Client action]
    ↓
localStorage.setItem("token", token)
localStorage.setItem("user", JSON.stringify(user))
    ↓
navigate("/dashboard")
```

---

## Error Response Examples

### Invalid Username/Password

```
Status: 401
Response:
{
  "message": "User tidak ditemukan"
  // atau
  "message": "Password salah"
}
```

### Duplicate Username/Email

```
Status: 409
Response:
{
  "message": "Username atau email sudah terdaftar"
}
```

### Missing Required Fields

```
Status: 400
Response:
{
  "message": "Username, email, dan password harus diisi"
}
```

### Server Error

```
Status: 500
Response:
{
  "message": "Terjadi kesalahan saat login"
}
```

---

## Security Measures

```
┌─────────────────────────────────┐
│ Client Side Security            │
├─────────────────────────────────┤
│ ✓ HTTPS (production)            │
│ ✓ Token in localStorage         │
│ ✓ Client-side form validation   │
│ ✓ Clear error messages          │
└─────────────────────────────────┘
        ↓ API Call ↓
┌─────────────────────────────────┐
│ Server Side Security            │
├─────────────────────────────────┤
│ ✓ Password hashing (bcryptjs)   │
│ ✓ JWT verification              │
│ ✓ Input validation              │
│ ✓ UNIQUE constraints            │
│ ✓ No password in response       │
│ ✓ Rate limiting (TODO)          │
│ ✓ CSRF protection (TODO)        │
└─────────────────────────────────┘
```

---

## Component Architecture

```
App Router Structure
│
├── /login
│   ├── page.tsx
│   └── LoginForm.tsx
│       ├── Form handling
│       ├── API call
│       ├── Error display
│       └── Redirect
│
├── /register
│   ├── page.tsx
│   └── RegisterForm.tsx
│       ├── Form validation
│       ├── Password match check
│       ├── API call
│       └── Success message
│
└── /dashboard
    └── page.tsx
        ├── useAuth() hook
        ├── Check authentication
        ├── Display user info
        └── Logout button
```

---

## Token Lifecycle

```
[Create]
JWT issued at 2024-01-15 10:30:00
with 7 day expiration
    ↓
[Store]
Client saves to localStorage
    ↓
[Use]
Include in API requests (future)
Authorization: Bearer <token>
    ↓
[Verify]
Server verifies signature & expiration
    ↓
[Expire]
Token expires after 7 days
User must login again
    ↓
[Remove]
User logout or token expired
Delete from localStorage
```

---

## File Dependencies

```
API Routes:
  register/route.ts
    ├── bcryptjs (password hashing)
    ├── crypto (randomUUID)
    ├── database.ts (runInsert)
    └── next/server (NextRequest, NextResponse)

  login/route.ts
    ├── bcryptjs (password verify)
    ├── jsonwebtoken (JWT generation)
    ├── database.ts (runQuery)
    └── next/server

Components:
  LoginForm.tsx
    ├── useAuth (future use)
    ├── useRouter (navigation)
    └── API fetch (/api/auth/login)

  RegisterForm.tsx
    ├── useRouter (navigation)
    └── API fetch (/api/auth/register)

Hooks:
  useAuth.ts
    ├── useEffect (check localStorage)
    ├── useRouter (redirect)
    ├── useState (user, token)
    └── localStorage API

Utils:
  auth.ts
    ├── jsonwebtoken (token verification)
    └── Exported for API route use
```
