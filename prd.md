1. Purpose

The Family Summer Expense Sharing App (working title: SummerSplit) is a lightweight mobile‑first web application that enables five families to log, track, and fairly split common summer holiday expenses. It targets casual, non‑technical users and must feel as simple as a shared spreadsheet—yet safer, faster, and nicer.

2. Goals & Success Metrics

Goal

Metric

Target

Fast onboarding

Time from hitting /2025 to first expense added

≤ 30 s

Accurate sharing

% of expenses with correct part allocation

≥ 95 %

Internationalisation

Full UI coverage in EN + FR

100 %

Mobile performance

Largest Contentful Paint (4 G)

< 2.5 s

Reliability

Error‑free sessions per 1000

≥ 998

3. Personas

Parent (Head of Family) – logs expenses and attendance; wants clarity on what they owe.

Teen / Child – views totals, rarely adds expenses.

Admin (Tech‑savvy parent) – sets annual password, maintains DB.

4. User Stories (Happy Path)

As a parent, I navigate to mypurchaseddomain.com/2025 and enter the summer password to unlock the app.

I choose my family icon and see two big buttons: ➕ Add Expense / 📄 All Expenses.

Adding an expense: date defaulted to today, amount in €, tap a category icon (Sport / Sortie / Alimentation / Cadeau / Other) → Save.

Viewing expenses: list filterable by family, date range, or category.

Editing my calendar: drag part values across days; colored heat‑map gives instant feedback.

How much do I owe? instantly shows my share based on parts and attendance.

5. Functional Requirements

#

Requirement

FR‑01

Password gate at /2025; 401 on wrong password.

FR‑02

Icon picker: 5 fixed family heads (config in DB).

FR‑03

Expense entry form with fields: date (calendar picker), amount (Euro, numeric), category (5 icons + custom text).

FR‑04

Calendar presence editor with drag‑to‑fill parts (1–4) per day, colour scale legend.

FR‑05

Multilingual UI toggle 🇫🇷 / 🇺🇸 stored in localStorage.

FR‑06

Hamburger menu routes to: Add Expense, All Expenses, My Expenses, Edit Calendar, How Much Do I Owe, Language.

FR‑07

All data persisted to AWS DB via REST JSON API; optimistic UI updates.

FR‑08

PWA installable shortcut (manifest + service worker).

6. Constraints & Out‑of‑Scope

No third‑party auth—single shared summer password only.

Only euro currency handling.

No complex budgets or refunds—simple equal split by parts.

7. Milestones

Date

Milestone

2025‑05‑15

v0.1 prototype (local storage)

2025‑06‑01

Backend & DB on AWS ready

2025‑06‑15

Beta test with family

2025‑07‑01

v1.0 production launch
