## 1. High‑Level Diagram

```
Browser (PWA) ──HTTPS──▶ AWS EC2 (Node 20 + Express + React static build)
                                        │
                                        ├──▶ RDS MySQL (expenses, presence, users)
                                        └──▶ Amazon S3 (backups)
```

## 2. Component Details

| Layer          | Tech                                                               | Notes                                                                     |
| -------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| Frontend       | React 18 (Vite) + TypeScript, Material UI v5                       | SPA served from `/static` by Express; service worker for offline caching. |
| Backend        | Node.js 20, Express, `sequelize` ORM                               | Pure REST (JSON); stateless; uses dotenv configs.                         |
| Auth           | Session cookie (HttpOnly, Secure) keyed by bcrypt(password) check. |                                                                           |
| DB             | Amazon RDS MySQL 8                                                 | Tables: `families`, `expenses`, `presence`, `translations`.               |
| Infrastructure | t4g.micro (ARM Graviton) Amazon Linux 2023                         | Nginx reverse‑proxy → Node (PM2).                                         |

## 3. Data Model (simplified)

```sql
CREATE TABLE families (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50),
  icon VARCHAR(50)
);

CREATE TABLE expenses (
  id INT AUTO_INCREMENT PRIMARY KEY,
  family_id INT NOT NULL,
  exp_date DATE NOT NULL,
  amount_cents INT NOT NULL,
  category ENUM('sport','sortie','alimentation','cadeau','other') NOT NULL,
  other_label VARCHAR(50) NULL,
  FOREIGN KEY (family_id) REFERENCES families(id)
);

CREATE TABLE presence (
  id INT AUTO_INCREMENT PRIMARY KEY,
  family_id INT,
  day DATE,
  parts TINYINT CHECK (parts BETWEEN 0 AND 4)
);
```

## 4. Key REST Endpoints

| Method | Path                     | Body / Query                                       | Purpose                 |
| ------ | ------------------------ | -------------------------------------------------- | ----------------------- |
| POST   | `/api/login`             | `{ password }`                                     | Returns session cookie. |
| GET    | `/api/families`          |                                                    | List families.          |
| POST   | `/api/expenses`          | `{ familyId, date, amount, category, otherLabel }` | Add.                    |
| GET    | `/api/expenses`          | `?from&to&familyId`                                | Filter list.            |
| PUT    | `/api/presence`          | `{ familyId, day, parts }`                         | Upsert parts.           |
| GET    | `/api/summary/:familyId` |                                                    | What do I owe?          |

## 5. Build & Deploy Flow

1. **GitHub Push** → branch triggers **Action**.
2. Action runs lint, tests, `vite build` → Docker image build.
3. On main merge, Action SSH‑deploys to EC2 (`docker compose up -d`).
