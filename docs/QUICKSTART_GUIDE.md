# Quick Start Development Guide
## Finance Calculator Hub - Local Setup with Portainer

**Version:** 1.0  
**Last Updated:** October 5, 2025  
**Target:** Developers setting up local environment

---

## Prerequisites

### Required Software

```bash
# Check if you have these installed:
node --version  # Should be v20+
npm --version   # Should be v10+
git --version   # Any recent version
docker --version  # Should be v24+
docker-compose --version  # Should be v2.20+
```

### Installation (MacOS)

```bash
# Install Homebrew (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js 20 LTS
brew install node@20
brew link node@20

# Install Docker Desktop for Mac
brew install --cask docker

# Start Docker Desktop (open from Applications)
# Wait for Docker to fully start before continuing
```

---

## Phase 1: Local Development Setup

### Step 1: Clone Repository

```bash
# Navigate to your projects directory
cd ~/Dev/Projects

# Clone the repository
git clone https://github.com/YOUR_USERNAME/finance-calculator.git
cd finance-calculator

# Create environment file
cp .env.example .env.local
```

### Step 2: Install Dependencies

```bash
# Install npm packages
npm install

# This will install:
# - Next.js 14+
# - TypeScript
# - @vanilla-extract packages
# - Prisma
# - React Hook Form + Zod
# - Recharts + Framer Motion
# - Radix UI components
# - and more...
```

### Step 3: Setup Database (Local PostgreSQL)

```bash
# Start PostgreSQL with Docker Compose
docker-compose -f docker-compose.dev.yml up -d db

# This starts:
# - PostgreSQL 16 on port 5432
# - Creates database: financecalc
# - Sets up initial schema

# Run Prisma migrations
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate

# (Optional) Open Prisma Studio to view database
npx prisma studio
# Opens at http://localhost:5555
```

### Step 4: Start Development Server

```bash
# Start Next.js development server
npm run dev

# Server starts at http://localhost:3000
# Features:
# ✓ Hot module replacement
# ✓ Fast refresh
# ✓ TypeScript checking
# ✓ Vanilla Extract CSS compilation
```

### Step 5: Verify Installation

Open your browser and visit:

```
http://localhost:3000
```

You should see the Finance Calculator homepage.

---

## Phase 2: Portainer Setup (Local Server Deployment)

### Step 1: Install Portainer

```bash
# Create Portainer volume
docker volume create portainer_data

# Install Portainer CE
docker run -d \
  -p 8000:8000 \
  -p 9000:9000 \
  --name=portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest

# Wait ~10 seconds for Portainer to start
```

### Step 2: Access Portainer Web UI

```bash
# Open Portainer in browser
open http://localhost:9000

# Or manually visit: http://localhost:9000
```

**First-time setup:**
1. Create admin username and password (save these!)
2. Select "Docker" environment
3. Connect to local Docker

### Step 3: Deploy Application via Portainer

#### Option A: Using Portainer Stacks (Recommended)

1. In Portainer, go to **Stacks** → **Add stack**
2. Name it: `finance-calculator`
3. **Upload** or **paste** your `docker-compose.yml`
4. Add environment variables (see below)
5. Click **Deploy the stack**

#### Option B: Using Git Repository

1. In Portainer, go to **Stacks** → **Add stack**
2. Choose **Git Repository**
3. Repository URL: `https://github.com/YOUR_USERNAME/finance-calculator.git`
4. Branch: `main`
5. Compose path: `docker-compose.yml`
6. Deploy!

### Step 4: Environment Variables

Add these in Portainer stack configuration:

```bash
# Node Environment
NODE_ENV=production

# Database
DATABASE_URL=postgresql://postgres:YOUR_DB_PASSWORD@db:5432/financecalc
DB_PASSWORD=YOUR_DB_PASSWORD

# Redis
REDIS_URL=redis://:YOUR_REDIS_PASSWORD@cache:6379
REDIS_PASSWORD=YOUR_REDIS_PASSWORD

# NextAuth.js (for future user auth)
NEXTAUTH_SECRET=generate-a-random-32-char-string
NEXTAUTH_URL=http://localhost:3000

# Google Analytics
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX

# Google AdSense (Phase 2)
NEXT_PUBLIC_ADSENSE_ID=ca-pub-XXXXXXXXXX
```

**Generate secure passwords:**

```bash
# On MacOS
openssl rand -base64 32
```

### Step 5: Monitor Containers

In Portainer:
1. Go to **Containers**
2. You should see:
   - `finance-calculator-app` (Next.js)
   - `finance-calculator-db` (PostgreSQL)
   - `finance-calculator-cache` (Redis)
   - `finance-calculator-nginx` (Nginx)

3. Check logs by clicking on container → **Logs**
4. Check stats: **Stats** tab shows CPU/Memory usage

---

## Project Structure

```
finance-calculator/
├── app/                          # Next.js app directory
│   ├── (marketing)/             # Marketing pages (SSG)
│   │   ├── page.tsx             # Homepage
│   │   └── layout.tsx
│   ├── calculators/             # Calculator pages
│   │   ├── sip/
│   │   │   ├── page.tsx
│   │   │   └── components/
│   │   │       ├── SIPForm.tsx
│   │   │       └── SIPForm.css.ts
│   │   ├── emi/
│   │   └── fd/
│   ├── api/                     # API routes
│   │   ├── calculate/
│   │   │   └── sip/route.ts
│   │   └── log/route.ts
│   └── layout.tsx               # Root layout
│
├── components/                   # Shared components
│   ├── ui/                      # Base UI components
│   │   ├── button/
│   │   │   ├── Button.tsx
│   │   │   └── Button.css.ts
│   │   ├── card/
│   │   ├── input/
│   │   └── slider/
│   └── calculators/             # Calculator-specific components
│
├── content/                      # MDX content
│   ├── blog/                    # Blog posts
│   │   ├── how-to-calculate-sip.mdx
│   │   └── emi-guide.mdx
│   └── calculators/             # Calculator descriptions
│       ├── sip-calculator.mdx
│       └── emi-calculator.mdx
│
├── lib/                          # Utility functions
│   ├── calculators/             # Calculator logic
│   │   ├── sip.ts
│   │   ├── emi.ts
│   │   └── fd.ts
│   ├── utils.ts
│   ├── prisma.ts                # Prisma client
│   ├── redis.ts                 # Redis client
│   └── mdx.ts                   # MDX utilities
│
├── styles/                       # Vanilla Extract styles
│   ├── theme.css.ts             # Design tokens
│   ├── global.css.ts            # Global styles
│   └── vars.css.ts              # CSS variables
│
├── prisma/                       # Database
│   ├── schema.prisma            # Database schema
│   └── migrations/              # Migration files
│
├── public/                       # Static assets
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── docker-compose.yml            # Production compose
├── docker-compose.dev.yml        # Development compose
├── Dockerfile                    # Next.js container
├── next.config.js               # Next.js config
├── tsconfig.json                # TypeScript config
├── package.json                 # Dependencies
└── .env.local                   # Environment variables
```

---

## Development Workflow

### Daily Development

```bash
# 1. Pull latest changes
git pull origin main

# 2. Install new dependencies (if package.json changed)
npm install

# 3. Run database migrations (if schema changed)
npx prisma migrate dev

# 4. Start development server
npm run dev

# 5. Make changes, test, commit
git add .
git commit -m "Your commit message"
git push origin your-branch
```

### Working with MDX Content

```typescript
// Create a new blog post
// File: content/blog/my-new-post.mdx

---
title: "How to Calculate SIP Returns"
description: "Complete guide to understanding SIP calculations"
date: "2025-10-05"
author: "Finance Team"
keywords: ["sip calculator", "mutual funds", "investment"]
---

# Introduction

Your content here with **markdown** formatting.

You can even include React components:

<Calculator type="sip" />

## More Sections

Continue writing...
```

### Working with Components

```typescript
// Create a new component
// File: components/ui/badge/Badge.tsx

import * as styles from './Badge.css';

interface BadgeProps {
  children: React.ReactNode;
  variant?: 'default' | 'success' | 'error';
}

export function Badge({ children, variant = 'default' }: BadgeProps) {
  return (
    <span className={styles.badge({ variant })}>
      {children}
    </span>
  );
}
```

```typescript
// Component styles
// File: components/ui/badge/Badge.css.ts

import { recipe } from '@vanilla-extract/recipes';
import { vars } from '@/styles/theme.css';

export const badge = recipe({
  base: {
    display: 'inline-flex',
    padding: `${vars.spacing.xs} ${vars.spacing.sm}`,
    borderRadius: vars.radius.md,
    fontSize: vars.fontSize.xs,
    fontWeight: vars.fontWeight.medium,
  },
  
  variants: {
    variant: {
      default: {
        backgroundColor: vars.colors.secondary,
        color: vars.colors.foreground,
      },
      success: {
        backgroundColor: vars.colors.success,
        color: vars.colors.successForeground,
      },
      error: {
        backgroundColor: vars.colors.error,
        color: vars.colors.errorForeground,
      },
    },
  },
});
```

### Calculator Development

```typescript
// 1. Create calculator logic
// File: lib/calculators/sip.ts

export interface SIPInput {
  monthlyInvestment: number;
  expectedReturn: number;
  timePeriod: number;
}

export interface SIPResult {
  maturityAmount: number;
  totalInvested: number;
  estimatedReturns: number;
  wealthGain: number;
}

export function calculateSIP(input: SIPInput): SIPResult {
  const { monthlyInvestment, expectedReturn, timePeriod } = input;
  const r = (expectedReturn / 12) / 100;
  const n = timePeriod * 12;
  
  const FV = monthlyInvestment * (((Math.pow(1 + r, n) - 1) / r) * (1 + r));
  const totalInvested = monthlyInvestment * n;
  const estimatedReturns = FV - totalInvested;
  const wealthGain = (estimatedReturns / totalInvested) * 100;
  
  return {
    maturityAmount: Math.round(FV),
    totalInvested: Math.round(totalInvested),
    estimatedReturns: Math.round(estimatedReturns),
    wealthGain: parseFloat(wealthGain.toFixed(2)),
  };
}
```

```typescript
// 2. Create API route
// File: app/api/calculate/sip/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { calculateSIP } from '@/lib/calculators/sip';

const sipSchema = z.object({
  monthlyInvestment: z.number().min(500).max(1000000),
  expectedReturn: z.number().min(1).max(30),
  timePeriod: z.number().min(1).max(40),
});

export async function POST(req: NextRequest) {
  try {
    const body = await req.json();
    const data = sipSchema.parse(body);
    const result = calculateSIP(data);
    
    return NextResponse.json({
      success: true,
      data: result,
    });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: 'Invalid input', details: error.errors },
        { status: 400 }
      );
    }
    
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

```typescript
// 3. Create UI component (see TECHNICAL_SPECIFICATION.md for full example)
// File: app/calculators/sip/components/SIPForm.tsx
```

---

## Testing

### Unit Tests

```bash
# Run tests
npm test

# Run tests in watch mode
npm test -- --watch

# Run tests with coverage
npm test -- --coverage
```

### E2E Tests (Coming in Phase 2)

```bash
# Install Playwright
npm install -D @playwright/test

# Run E2E tests
npm run test:e2e
```

---

## Building for Production

### Local Build

```bash
# Build the application
npm run build

# Start production server
npm start

# Test at http://localhost:3000
```

### Docker Build

```bash
# Build Docker image
docker build -t finance-calculator:latest .

# Run container
docker run -p 3000:3000 finance-calculator:latest
```

---

## Deployment Checklist

### Pre-Deployment

- [ ] All tests passing
- [ ] No TypeScript errors
- [ ] No ESLint errors
- [ ] Environment variables configured
- [ ] Database migrations applied
- [ ] SSL certificates ready
- [ ] Cloudflare DNS configured

### Deployment

- [ ] Build Docker images
- [ ] Deploy to Portainer
- [ ] Run database migrations
- [ ] Verify all containers running
- [ ] Check logs for errors
- [ ] Test all calculators
- [ ] Verify analytics tracking
- [ ] Check page load speeds

### Post-Deployment

- [ ] Monitor error logs
- [ ] Check performance metrics
- [ ] Verify SEO meta tags
- [ ] Test from different devices
- [ ] Submit sitemap to Google
- [ ] Monitor uptime

---

## Troubleshooting

### Common Issues

#### Port Already in Use

```bash
# Kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Or use a different port
PORT=3001 npm run dev
```

#### Database Connection Error

```bash
# Check if PostgreSQL is running
docker ps | grep postgres

# Restart database
docker-compose -f docker-compose.dev.yml restart db

# Check database logs
docker logs finance-calculator-db
```

#### Module Not Found Error

```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Clear Next.js cache
rm -rf .next
npm run build
```

#### Prisma Client Out of Sync

```bash
# Regenerate Prisma Client
npx prisma generate

# Reset database (WARNING: deletes all data)
npx prisma migrate reset
```

#### Docker Permission Error

```bash
# On MacOS, ensure Docker Desktop is running
# Check Docker is accessible
docker ps

# If permission denied, restart Docker Desktop
```

---

## Useful Commands Reference

### Development

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm start            # Start production server
npm test             # Run tests
npm run lint         # Run ESLint
npm run type-check   # TypeScript checking
```

### Database

```bash
npx prisma studio            # Open database GUI
npx prisma migrate dev       # Create and apply migration
npx prisma migrate deploy    # Apply migrations (production)
npx prisma generate          # Generate Prisma Client
npx prisma db push           # Push schema (dev only)
npx prisma db seed           # Run seed script
```

### Docker

```bash
docker-compose up -d                    # Start all containers
docker-compose down                     # Stop all containers
docker-compose logs -f web              # View logs
docker-compose restart web              # Restart service
docker-compose ps                       # List containers
docker exec -it finance-calculator-db bash  # Shell into container
```

### Git

```bash
git status                   # Check status
git add .                    # Stage changes
git commit -m "message"      # Commit
git push origin main         # Push to GitHub
git pull origin main         # Pull changes
git branch feature-name      # Create branch
git checkout feature-name    # Switch branch
```

---

## Next Steps

1. **Complete Phase 1 Setup**: Follow this guide to get local development working
2. **Read Technical Specification**: See `docs/TECHNICAL_SPECIFICATION.md` for architecture details
3. **Start Building**: Begin with SIP calculator component
4. **Deploy to Portainer**: Test containerized deployment locally
5. **Domain & SSL**: Setup domain and configure Cloudflare

---

## Support & Resources

### Documentation
- This guide (Quick Start)
- [Technical Specification](./TECHNICAL_SPECIFICATION.md)
- [Document Alignment Review](./DOCUMENT_ALIGNMENT_REVIEW.md)
- [SEO Keyword Research](./SEO_KEYWORD_RESEARCH.md)

### External Resources
- [Next.js Docs](https://nextjs.org/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Vanilla Extract Docs](https://vanilla-extract.style/)
- [Prisma Docs](https://www.prisma.io/docs/)
- [Docker Docs](https://docs.docker.com/)
- [Portainer Docs](https://docs.portainer.io/)

### Getting Help
- Check existing documentation first
- Search GitHub issues
- Ask in team Slack/Discord
- Create detailed issue reports

---

**Last Updated:** October 5, 2025  
**Version:** 1.0  
**Maintainer:** Development Team
