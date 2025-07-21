## 1. Performance

- **Time‑to‑Interactive** < 3 s on budget Android (4 G).
- API P99 latency < 200 ms (EU‑West‑1).

## 2. Availability & Reliability

- Target **99.8 %** uptime (single AZ EC2 + daily AMI backup).
- Automatic EC2 reboot on failure using AWS Auto‑Recovery.

## 3. Security

- HTTPS everywhere (ACM cert, TLS 1.3).
- Password & DB credentials stored as bcrypt hashes and AWS SSM Parameter Store secrets.
- IAM least‑privilege; no public DB endpoints.

## 4. Internationalisation (i18n)

- **react‑i18next**; languages `en`, `fr`.
- Language file coverage enforced by CI.

## 5. Cost

- Monthly AWS budget ≤ 10 € (t4g.micro EC2 reserve, db.t3.micro RDS, free tier Route 53 record set).

## 6. Maintainability

- 100 % IaC (Terraform) optional phase‑2.
- Prettier/ESLint + husky pre‑commit.

## 7. UX / Aesthetics

- Google Material colour palette; WCAG 2.1 AA contrast.
- Mobile‑first; pointer & touch support.
