# AWS Quick Reference Guide
### End-to-End Cloud Solution Design & Deployment | Group 13 | AWS Cloud Computing Gen 4 Cohort

## Introduction
This is a searchable, single-page reference guide for common AWS services. Built to solve the problem new AWS learners face: juggling 10+ documentation tabs just to answer a basic question about which service to use and how it fits together, and the app itself is deployed on the very architecture it describes, making it a working demonstration of AWS best practices.

**Live site:** [https://group13capstone.online](https://group13capstone.online)

---

## What it does

| Page | URL | Purpose |
| --- | --- | --- |
| Home | `/` | Hero search, 8 category cards, 6 featured services, why-this-guide section |
| Services | `/pages/services.html` | All 35+ AWS services — searchable and filterable by category |
| Cheatsheet | `/pages/cheatsheet.html` | Ports, regions, free tier limits, CLI commands, IAM structure |
| Getting Started | `/pages/getting-started.html` | 10-step beginner guide from account creation to 3-tier architecture |

---

## Run locally

No build step required. The site is plain HTML, CSS, and vanilla JavaScript.

### Option 1 — Python (recommended)

```bash
python3 -m http.server 8080
```

Then open [http://localhost:8080](http://localhost:8080) in your browser.

### Option 2 — Node.js

```bash
npx serve .
```

### Option 3 — VS Code

Install the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension, right-click `index.html` → *Open with Live Server*.

> All pages use relative paths so any static file server works. Do **not** open `index.html` directly as a `file://` URL — the CSS `@import` chain won't load in some browsers.

---

## Project structure

```text
Group-13-Capstone-Project/
├── index.html                  # Landing page
├── pages/
│   ├── services.html           # 35+ service cards with live search
│   ├── cheatsheet.html         # Quick reference tables
│   └── getting-started.html    # Step-by-step beginner guide
├── assets/
│   ├── css/
│   │   ├── style.css           # Entry point — imports all partials
│   │   ├── variables.css       # CSS custom properties (colours, radii, shadows)
│   │   ├── base.css            # Reset, global styles, layout utilities
│   │   ├── layout.css          # Header, nav, hero, footer, CTA, buttons
│   │   ├── components.css      # Category cards, service cards, cheatsheet, guide
│   │   └── utilities.css       # Icon/badge tokens, why/next cards, responsive
│   └── images/
├── .github/
│   └── workflows/
│       └── deploy.yml          # CI/CD — S3 sync + SSM deploy on push to main
├── ARCHITECTURE.md             # Full AWS infrastructure documentation
└── README.md
```

---

## AWS infrastructure

The site runs on a fully hardened AWS stack in `us-east-1`.

```text
Browser → Route 53 → CloudFront → ALB → EC2 (Apache)
                              └──────→ S3  (static assets /assets/*)
```

| Layer | Service | Detail |
| --- | --- | --- |
| DNS | Route 53 | A alias records for apex + www → CloudFront |
| CDN | CloudFront `ECUXP1DUSC95X` | HTTPS enforced, ACM cert, custom origin header |
| Certificates | ACM | `group13capstone.online` + `www` SAN, DNS-validated |
| Load balancer | ALB `group13-alb` | Enforces `X-Origin-Verify` header — returns 403 otherwise |
| Compute | EC2 t3.micro, Amazon Linux 2023, Apache 2.4.67 | Managed by Auto Scaling Group |
| Auto Scaling | ASG `Group13-ASG` | Scale out at CPU >70%, scale in at CPU <30% |
| Static assets | S3 `group13capstonebucket` | OAC — only CloudFront can read objects |

Full infrastructure documentation including Mermaid architecture diagram: [ARCHITECTURE.md](ARCHITECTURE.md)

---

## CI/CD — deploy workflow

Pushing to `main` triggers GitHub Actions automatically:

1. **Sync to S3** — uploads `assets/`, `index.html`, `pages/` to `group13capstonebucket`
2. **Invalidate CloudFront** — purges `/*` so edge caches serve fresh content
3. **Deploy to EC2** — discovers all healthy ALB target instances at runtime, runs SSM `AWS-RunShellScript` on each to sync files from S3 to `/var/www/html/` and reload Apache
4. **Health check** — curls `https://group13capstone.online/health` through the full CloudFront → ALB → EC2 path

### GitHub secrets required

| Secret | Value |
| --- | --- |
| `AWS_ACCESS_KEY_ID` | Access key for IAM user `github-deploy` |
| `AWS_SECRET_ACCESS_KEY` | Secret key for IAM user `github-deploy` |

The deploy user has least-privilege IAM permissions: S3 sync on the project bucket, CloudFront invalidation on the project distribution, SSM send-command on any EC2 instance in the account, and ELB DescribeTargetHealth.

---

## Team Members — Group 13
| Name | Role | Responsibilities |
|---|---|---|
| Vera | AWS Admin | IAM, VPC, ALB, Security groups, AWS Budgets |
| Othniel | App Developer | Web app build, EC2 deployment, S3 assets, Deploy Scripts |
| Kelvin | Security & CDN | CloudFront, ACM, S3 OAC, GitHub Actions, Cloudwatch |
| Betty | Project Manager & Docs | Architecture diagram, README, Slides, Screenshots, IAM review |


