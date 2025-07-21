## 1. Repository Layout

```
root/
 ├─ src/            # React source
 ├─ server/         # Express API
 ├─ prisma/         # (optional) schema
 ├─ .github/
 │   ├─ workflows/
 │   │   ├─ ci.yml  # build & test
 │   │   └─ prod.yml # deploy on main
 │   └─ PULL_REQUEST_TEMPLATE.md
 └─ README.md
```

## 2. Branching Strategy

- **main** – protected, prod deployment.
- **develop** – integration of feature branches.
- **feature/**\* – short‑lived, squash‑merged.

## 3. Git Hooks

- `pre‑commit`: `eslint --fix && prettier --check && npm test`.
- `commit‑msg`: conventional commits enforced via `commitlint`.

## 4. GitHub Actions Highlights

```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm ci
      - run: npm run test -- --coverage
      - run: npm run build
```

`prod.yml` adds steps:

- build Docker image, push to GHCR.
- SSH into EC2 using `secrets.EC2_KEY` & run `docker-compose pull && docker-compose up -d`.

## 5. Issue & PR Labels

- `type:feature`, `type:bug`, `i18n`, `aws`, `good first issue`.

