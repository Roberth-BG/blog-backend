# 📝 Blog Backend

REST API for a full-stack blog platform built with **Spring Boot**, **Java 17** and **MySQL**.
Frontend built with **Angular** (separate repository).

---

## 🚀 Tech Stack

| Layer | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot 3.x |
| Security | Spring Security + JWT |
| ORM | Spring Data JPA / Hibernate |
| Database | MySQL 8 |
| Documentation | Swagger / OpenAPI 3 |
| Build Tool | Maven |

---

## 📁 Project Structure

```
blog-backend/
│
├── src/main/java/com/blog/
│   ├── BlogApplication.java
│   ├── controllers/        ← REST endpoints
│   ├── entities/           ← JPA entities (DB tables)
│   ├── dtos/
│   │   ├── request/        ← incoming data
│   │   └── response/       ← outgoing data
│   ├── repositories/       ← JPA repositories
│   ├── services/           ← business logic
│   ├── mappers/            ← entity ↔ DTO conversion
│   ├── config/             ← Security, Swagger, CORS, MVC
│   ├── security/           ← JWT filter, util, UserDetails
│   ├── routes/             ← centralized API route constants
│   ├── shared/
│   │   ├── response/       ← ApiResponse, PagedResponse
│   │   ├── exceptions/     ← custom exceptions + global handler
│   │   └── constants/      ← Messages, Roles
│   └── utils/              ← Slug, File, Pagination, Date helpers
│
├── src/main/resources/
│   ├── application.properties
│   ├── application-dev.properties
│   └── application-prod.properties
│
├── uploads/                ← file storage (git ignored)
├── pom.xml
├── .env                    ← environment variables (git ignored)
└── .gitignore
```

---

## 🗄️ Database Design

MySQL database with **logical deletion** (`activo` field) and **multimedia support**.

### Tables

| Table | Description |
|---|---|
| `roles` | User roles: ADMIN, EDITOR, LECTOR |
| `usuarios` | Users with email verification and login security |
| `usuario_roles` | Many-to-many: users ↔ roles |
| `categorias` | Post categories with slug |
| `tags` | Post tags with slug |
| `posts` | Blog posts (LONGTEXT content, no limit) |
| `post_tags` | Many-to-many: posts ↔ tags |
| `post_archivos` | Multimedia files attached to posts |
| `comentarios` | Post comments with moderation |

### Key design decisions

- **Logical deletion** via `activo BOOLEAN` — records are never physically deleted
- **Post content** uses `LONGTEXT` (~4 billion chars) — no practical writing limit
- **Files are not stored in the DB** — only the URL/path is saved; files go to disk
- **Two environments**: `blog_dev` (development) and `blog_prod` (production)

### File storage structure

```
uploads/
├── usuarios/avatars/{id}/avatar.jpg
├── categorias/{id}/imagen.jpg
└── posts/
    ├── portadas/{id}/portada.jpg
    └── archivos/{id}/
        ├── imagenes/
        ├── videos/
        ├── audios/
        └── documentos/
```

### Database triggers

| Trigger | Action |
|---|---|
| `tr_usuarios_verificado` | Cleans verification code and sets `activo=TRUE` when email is verified |
| `tr_posts_published_at` | Auto-sets `published_at` when post status changes to PUBLISHED |

---

## 🔐 Authentication & Roles

JWT-based authentication with role-based access control.

| Role | Permissions |
|---|---|
| `ADMIN` | Full access: manage users, posts, comments, categories |
| `EDITOR` | Create, edit and publish own posts; moderate comments |
| `LECTOR` | Read published posts and comment |

### Security features

- BCrypt password hashing
- Email verification with expiring code (6-char code, X min TTL)
- Failed login tracking with temporary account lockout
- JWT token with role claims

---

## 📡 API Endpoints (planned)

| Method | Route | Role | Description |
|---|---|---|---|
| POST | `/api/auth/registro` | Public | Register new user |
| POST | `/api/auth/login` | Public | Login, returns JWT |
| POST | `/api/auth/verificar` | Public | Verify email with code |
| GET | `/api/posts` | Public | List published posts |
| GET | `/api/posts/slug/{slug}` | Public | Get post by slug |
| POST | `/api/posts` | EDITOR/ADMIN | Create post |
| PUT | `/api/posts/{id}` | EDITOR/ADMIN | Update post |
| DELETE | `/api/posts/{id}` | ADMIN | Deactivate post |
| GET | `/api/usuarios` | ADMIN | List all users |
| PUT | `/api/usuarios/{id}/activar` | ADMIN | Activate/deactivate user |
| POST | `/api/upload/avatar` | Auth | Upload user avatar |
| POST | `/api/upload/post/{id}/portada` | EDITOR/ADMIN | Upload post cover image |
| GET | `/api/categorias` | Public | List categories |
| GET | `/api/tags` | Public | List tags |
| GET | `/api/comentarios` | ADMIN | List comments for moderation |

Full interactive documentation available at `/swagger-ui.html` when running locally.

---

## ⚙️ Getting Started

### Prerequisites

- Java 17+
- Maven 3.8+
- MySQL 8+

### 1. Clone the repository

```bash
git clone https://github.com/tu-usuario/blog-backend.git
cd blog-backend
```

### 2. Create the databases

```sql
CREATE DATABASE blog_dev  CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE DATABASE blog_prod CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 3. Configure environment variables

Create a `.env` file in the root (never commit this file):

```env
DB_USER=root
DB_PASSWORD=your_password
JWT_SECRET=your-256-bit-secret
```

### 4. Run in development

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

### 5. Access Swagger UI

```
http://localhost:8080/swagger-ui.html
```

---

## 🗂️ Development Roadmap

### ✅ Phase 1 — Database (completed)
- [x] Database schema design (9 tables)
- [x] Logical deletion strategy
- [x] Multimedia file storage design
- [x] Seed data for testing
- [x] Database triggers

### 🔄 Phase 2 — Backend (in progress)
- [ ] Project setup (pom.xml, application.properties)
- [ ] JPA entities
- [ ] Repositories
- [ ] DTOs (request/response)
- [ ] Mappers
- [ ] Services
- [ ] Controllers
- [ ] JWT security
- [ ] File upload
- [ ] Email verification
- [ ] Swagger documentation

### ⏳ Phase 3 — Frontend (upcoming)
- [ ] Angular project setup
- [ ] Authentication module
- [ ] Blog public view
- [ ] Admin panel

---

## 🤝 Contributing

This is a personal learning project. Feel free to fork and adapt it.

---

## 📄 License

MIT License
