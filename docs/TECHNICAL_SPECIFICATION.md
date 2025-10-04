# Technical Specification Document
## Finance Calculator Hub - Modern Architecture
### Containerized Next.js Application

**Document Version:** 2.0  
**Last Updated:** October 4, 2025  
**Status:** Ready for Development  
**Architecture:** Modern Containerized Stack  
**Deployment:** Portainer (Local Server) → Cloud (Future)

---

## Executive Summary

### Vision
Build a **world-class financial calculator platform** with:
- 🎨 **Beautiful UI** - Inspired by Stripe, Linear, Vercel, Framer
- ⚡ **Blazing Fast** - <1s page loads, instant calculations
- 🏗️ **Modern Stack** - Next.js 14+, TypeScript, Tailwind CSS
- 🐳 **Containerized** - Docker containers managed via Portainer
- 📱 **Mobile-First** - Responsive design, PWA capabilities
- 🎯 **SEO Optimized** - Server-side rendering, perfect Lighthouse scores

### Tech Philosophy
- **Quality over quantity** - 3 perfect calculators > 10 mediocre ones
- **Performance is a feature** - Sub-second load times, 60fps animations
- **Design excellence** - Every pixel matters, delightful interactions
- **Developer experience** - Hot reload, type safety, modern tooling

---

## Table of Contents

1. [Technology Stack](#1-technology-stack)
2. [Architecture Overview](#2-architecture-overview)
3. [Container Structure](#3-container-structure)
4. [Database Design](#4-database-design)
5. [Frontend Architecture](#5-frontend-architecture)
6. [Backend API](#6-backend-api)
7. [UI/UX Design System](#7-uiux-design-system)
8. [Performance Strategy](#8-performance-strategy)
9. [Deployment Setup](#9-deployment-setup)
10. [Development Workflow](#10-development-workflow)

---

## 1. Technology Stack

### 1.1 Core Stack (Modern & Fast)

```yaml
Frontend Framework:
  Framework: Next.js 14+ (App Router)
  Language: TypeScript 5+
  Styling: Tailwind CSS 3+ + shadcn/ui
  State: Zustand (lightweight, <1KB)
  Forms: React Hook Form + Zod
  Charts: Recharts + Framer Motion
  
Why Next.js:
  ✓ Server-side rendering (SSR) for SEO
  ✓ Static site generation (SSG) for performance
  ✓ Built-in image optimization
  ✓ API routes (no separate backend needed)
  ✓ Hot module replacement (instant dev updates)
  ✓ Best-in-class developer experience
  ✓ Used by: Vercel, TikTok, Twitch, Hulu, Nike

Backend:
  Runtime: Node.js 20 LTS
  API: Next.js API Routes (REST)
  Database: PostgreSQL 16 (modern, reliable, fast)
  ORM: Prisma (type-safe, auto-completion)
  Cache: Redis 7 (in-memory caching)
  Search: MeiliSearch (optional, Phase 2)

Infrastructure:
  Containerization: Docker + Docker Compose
  Management: Portainer CE (web UI)
  Web Server: Nginx (reverse proxy, SSL)
  Monitoring: Grafana + Prometheus (optional)
  
Deployment Targets:
  Phase 1: Local Server (Portainer)
  Phase 2: Cloud Options:
    - Vercel (best for Next.js, $20/mo)
    - Railway ($5-20/mo)
    - DigitalOcean App Platform ($12/mo)
    - AWS Lightsail Containers ($7/mo)
```

### 1.2 UI Component Library

```yaml
Component System: shadcn/ui
Why: 
  ✓ Beautiful by default (inspired by Linear, Vercel)
  ✓ Radix UI primitives (accessible, unstyled)
  ✓ Tailwind CSS (utility-first, customizable)
  ✓ Copy-paste components (no npm install)
  ✓ TypeScript native
  ✓ Dark mode built-in

Key Components We'll Use:
  - Button (with variants, sizes, loading states)
  - Card (glassmorphism, shadows, borders)
  - Input (with icons, validation states)
  - Slider (smooth animations, tooltips)
  - Tabs (calculator types)
  - Dialog (modals for detailed results)
  - Toast (notifications)
  - Sheet (mobile sidebar)
  - Accordion (FAQs)

Animation Library: Framer Motion
Why:
  ✓ Smooth 60fps animations
  ✓ Gesture support (drag, swipe)
  ✓ Layout animations (magic move)
  ✓ Spring physics (natural feel)
  
Chart Library: Recharts + D3.js
Why:
  ✓ Responsive charts
  ✓ Smooth animations
  ✓ Customizable
  ✓ TypeScript support
```

### 1.3 Design Inspiration

```yaml
Visual References:
  Stripe.com:
    - Clean typography
    - Subtle gradients
    - Smooth animations
    - Perfect spacing
  
  Linear.app:
    - Keyboard shortcuts
    - Command palette
    - Instant feedback
    - Dark mode excellence
  
  Vercel.com:
    - Brutalist design
    - Bold typography
    - Fast transitions
    - Minimalist UI
  
  Framer.com:
    - Interactive prototypes
    - Smooth animations
    - Modern gradients
    - Glass morphism

Design Principles:
  1. Clarity > Cleverness
  2. Fast > Flashy
  3. Consistent > Creative
  4. Simple > Complex
```

---

## 2. Architecture Overview

### 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    USER DEVICES                          │
│  📱 Mobile (70%)  💻 Desktop (25%)  🖥️ Tablet (5%)     │
└─────────────────────────────────────────────────────────┘
                         ↓ HTTPS
┌─────────────────────────────────────────────────────────┐
│                  CLOUDFLARE CDN (Free)                   │
│  • Global edge caching                                   │
│  • DDoS protection                                       │
│  • SSL/TLS                                               │
│  • Web Application Firewall                              │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│              YOUR LOCAL SERVER (Portainer)               │
│                                                          │
│  ┌────────────────────────────────────────────────────┐ │
│  │            Nginx Reverse Proxy                     │ │
│  │  • SSL termination                                 │ │
│  │  • Load balancing                                  │ │
│  │  • Gzip/Brotli compression                        │ │
│  │  Port: 80 (HTTP), 443 (HTTPS)                     │ │
│  └────────────────────────────────────────────────────┘ │
│                         ↓                                │
│  ┌────────────────────────────────────────────────────┐ │
│  │        Next.js Application (Container 1)           │ │
│  │  • Server-side rendering                           │ │
│  │  • API routes                                      │ │
│  │  • Static optimization                             │ │
│  │  Port: 3000 (internal)                            │ │
│  │  Image: node:20-alpine                            │ │
│  └────────────────────────────────────────────────────┘ │
│                         ↓                                │
│  ┌────────────────────────────────────────────────────┐ │
│  │        PostgreSQL Database (Container 2)           │ │
│  │  • User data                                       │ │
│  │  • Calculator logs                                 │ │
│  │  • Analytics                                       │ │
│  │  Port: 5432 (internal)                            │ │
│  │  Image: postgres:16-alpine                        │ │
│  │  Volume: /data/postgres                           │ │
│  └────────────────────────────────────────────────────┘ │
│                         ↓                                │
│  ┌────────────────────────────────────────────────────┐ │
│  │           Redis Cache (Container 3)                │ │
│  │  • Session storage                                 │ │
│  │  • API response cache                              │ │
│  │  • Rate limiting                                   │ │
│  │  Port: 6379 (internal)                            │ │
│  │  Image: redis:7-alpine                            │ │
│  └────────────────────────────────────────────────────┘ │
│                                                          │
│  ┌────────────────────────────────────────────────────┐ │
│  │        Portainer Agent (Container 4)               │ │
│  │  • Container management                            │ │
│  │  • Monitoring                                      │ │
│  │  • Logs viewing                                    │ │
│  │  Port: 9000 (web UI)                              │ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### 2.2 Request Flow

```
1. User visits financecalc.in
   ↓
2. Cloudflare CDN (cache check)
   ↓
3. Your Server (Nginx)
   ↓
4. Next.js App
   ├─→ Static pages (cached, instant)
   ├─→ API routes (calculator logic)
   └─→ Database queries (PostgreSQL)
   ↓
5. Redis cache (fast reads)
   ↓
6. Response back to user

Total time: <500ms (cached) or <1.5s (uncached)
```

---

## 3. Container Structure

### 3.1 Docker Compose Configuration

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Next.js Application
  web:
    container_name: finance-calculator-app
    build:
      context: ./app
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@db:5432/financecalc
      - REDIS_URL=redis://cache:6379
      - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
      - NEXTAUTH_URL=https://financecalc.in
    depends_on:
      - db
      - cache
    restart: unless-stopped
    networks:
      - finance-network
    volumes:
      - ./app:/app
      - /app/node_modules
      - /app/.next
    labels:
      - "com.portainer.service=finance-calculator"

  # PostgreSQL Database
  db:
    container_name: finance-calculator-db
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=financecalc
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - PGDATA=/var/lib/postgresql/data/pgdata
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
    restart: unless-stopped
    networks:
      - finance-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis Cache
  cache:
    container_name: finance-calculator-cache
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --appendonly yes --requirepass ${REDIS_PASSWORD}
    volumes:
      - redis-data:/data
    restart: unless-stopped
    networks:
      - finance-network
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Nginx Reverse Proxy
  nginx:
    container_name: finance-calculator-nginx
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
      - nginx-cache:/var/cache/nginx
    depends_on:
      - web
    restart: unless-stopped
    networks:
      - finance-network

volumes:
  postgres-data:
    driver: local
  redis-data:
    driver: local
  nginx-cache:
    driver: local

networks:
  finance-network:
    driver: bridge
```

### 3.2 Dockerfile (Next.js App)

```dockerfile
# Dockerfile
FROM node:20-alpine AS base

# Install dependencies only when needed
FROM base AS deps
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Install dependencies based on the preferred package manager
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Environment variables for build time
ENV NEXT_TELEMETRY_DISABLED 1

# Build Next.js
RUN npm run build

# Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

# Set the correct permission for prerender cache
RUN mkdir .next
RUN chown nextjs:nodejs .next

# Automatically leverage output traces to reduce image size
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000
ENV HOSTNAME "0.0.0.0"

CMD ["node", "server.js"]
```

### 3.3 Nginx Configuration

```nginx
# nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 2048;
    use epoll;
    multi_accept on;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;

    # Performance
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    client_max_body_size 20M;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml text/javascript 
               application/json application/javascript application/xml+rss 
               application/rss+xml font/truetype font/opentype 
               application/vnd.ms-fontobject image/svg+xml;

    # Brotli compression (if module available)
    brotli on;
    brotli_comp_level 6;
    brotli_types text/plain text/css text/xml text/javascript 
                 application/json application/javascript application/xml+rss;

    # Cache settings
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=STATIC:10m 
                     inactive=7d use_temp_path=off;

    # Upstream Next.js
    upstream nextjs_upstream {
        server web:3000;
        keepalive 64;
    }

    # HTTP to HTTPS redirect
    server {
        listen 80;
        server_name financecalc.in www.financecalc.in;
        
        location /.well-known/acme-challenge/ {
            root /var/www/certbot;
        }
        
        location / {
            return 301 https://$server_name$request_uri;
        }
    }

    # HTTPS server
    server {
        listen 443 ssl http2;
        server_name financecalc.in www.financecalc.in;

        # SSL certificates (Let's Encrypt)
        ssl_certificate /etc/nginx/ssl/fullchain.pem;
        ssl_certificate_key /etc/nginx/ssl/privkey.pem;
        
        # SSL configuration
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 10m;

        # Security headers
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;
        add_header Referrer-Policy "strict-origin-when-cross-origin" always;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

        # Next.js static files with caching
        location /_next/static {
            proxy_pass http://nextjs_upstream;
            proxy_cache STATIC;
            proxy_cache_valid 200 7d;
            proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
            add_header Cache-Control "public, max-age=31536000, immutable";
            add_header X-Cache-Status $upstream_cache_status;
        }

        # Public static files
        location /public {
            proxy_pass http://nextjs_upstream;
            proxy_cache STATIC;
            proxy_cache_valid 200 1d;
            add_header Cache-Control "public, max-age=86400";
        }

        # All other requests
        location / {
            proxy_pass http://nextjs_upstream;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Port $server_port;
            
            # Timeouts
            proxy_connect_timeout 60s;
            proxy_send_timeout 60s;
            proxy_read_timeout 60s;
        }
    }
}
```

---

## 4. Database Design

### 4.1 Prisma Schema

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// User model (for Phase 3 - user accounts)
model User {
  id            String    @id @default(cuid())
  email         String    @unique
  name          String?
  image         String?
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
  
  calculations  SavedCalculation[]
  
  @@map("users")
}

// Calculator usage logs
model CalculatorLog {
  id            String    @id @default(cuid())
  calculatorType String   @db.VarChar(50)
  inputData     Json
  resultData    Json?
  userIp        String?   @db.VarChar(45)
  userAgent     String?   @db.Text
  sessionId     String?   @db.VarChar(64)
  pageUrl       String?   @db.VarChar(500)
  createdAt     DateTime  @default(now())
  
  @@index([calculatorType])
  @@index([createdAt])
  @@index([sessionId])
  @@map("calculator_logs")
}

// Saved calculations (Phase 3)
model SavedCalculation {
  id             String    @id @default(cuid())
  userId         String
  calculatorType String    @db.VarChar(50)
  title          String?   @db.VarChar(255)
  inputData      Json
  resultData     Json
  isPrivate      Boolean   @default(true)
  createdAt      DateTime  @default(now())
  updatedAt      DateTime  @updatedAt
  
  user           User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  
  @@index([userId])
  @@index([calculatorType])
  @@index([createdAt])
  @@map("saved_calculations")
}

// Interest rates cache
model InterestRate {
  id            String    @id @default(cuid())
  bankName      String    @db.VarChar(100)
  productType   String    @db.VarChar(50)
  rateType      String    @db.VarChar(20)
  tenureMonths  Int?
  interestRate  Decimal   @db.Decimal(5, 2)
  effectiveFrom DateTime  @db.Date
  effectiveTo   DateTime? @db.Date
  lastUpdated   DateTime  @default(now()) @updatedAt
  sourceUrl     String?   @db.Text
  isActive      Boolean   @default(true)
  
  @@unique([bankName, productType, rateType, tenureMonths, effectiveFrom])
  @@index([productType])
  @@index([bankName])
  @@index([isActive])
  @@map("interest_rates")
}

// Content metrics
model ContentMetric {
  id                  String   @id @default(cuid())
  pageSlug            String   @db.VarChar(255)
  metricDate          DateTime @db.Date
  pageviews           Int      @default(0)
  uniqueVisitors      Int      @default(0)
  avgTimeOnPage       Int      @default(0)
  bounceRate          Decimal  @db.Decimal(5, 2)
  calculatorInteractions Int   @default(0)
  revenueGenerated    Decimal  @db.Decimal(10, 2) @default(0)
  
  @@unique([pageSlug, metricDate])
  @@index([metricDate])
  @@map("content_metrics")
}
```

### 4.2 Database Migrations

```bash
# Initialize Prisma
npx prisma init

# Create migration
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate

# Deploy to production
npx prisma migrate deploy

# Prisma Studio (database GUI)
npx prisma studio
```

---

## 5. Frontend Architecture

### 5.1 Project Structure

```
app/
├── (marketing)/              # Marketing pages (SSG)
│   ├── page.tsx             # Homepage
│   ├── about/
│   ├── blog/
│   └── layout.tsx
│
├── calculators/              # Calculator pages (SSR + CSR)
│   ├── sip/
│   │   ├── page.tsx
│   │   └── components/
│   │       ├── SIPForm.tsx
│   │       ├── SIPResults.tsx
│   │       └── SIPChart.tsx
│   ├── emi/
│   ├── fd/
│   └── layout.tsx
│
├── api/                      # API routes
│   ├── calculate/
│   │   ├── sip/route.ts
│   │   ├── emi/route.ts
│   │   └── fd/route.ts
│   ├── log/route.ts
│   └── rates/route.ts
│
├── components/               # Shared components
│   ├── ui/                  # shadcn/ui components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── input.tsx
│   │   ├── slider.tsx
│   │   └── ...
│   ├── calculators/         # Calculator components
│   │   ├── CalculatorShell.tsx
│   │   ├── ResultCard.tsx
│   │   └── ChartContainer.tsx
│   ├── layout/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── Sidebar.tsx
│   └── shared/
│       ├── Logo.tsx
│       ├── ThemeToggle.tsx
│       └── CommandPalette.tsx
│
├── lib/                      # Utility functions
│   ├── calculators/
│   │   ├── sip.ts
│   │   ├── emi.ts
│   │   └── fd.ts
│   ├── utils.ts
│   ├── prisma.ts
│   ├── redis.ts
│   └── validations.ts
│
├── styles/
│   └── globals.css
│
├── types/
│   ├── calculator.ts
│   └── api.ts
│
└── config/
    ├── site.ts
    └── calculators.ts

public/
├── images/
├── icons/
└── fonts/

prisma/
├── schema.prisma
└── migrations/

.env.local
.env.production
next.config.js
tailwind.config.ts
tsconfig.json
package.json
docker-compose.yml
Dockerfile
```

### 5.2 Key Component Examples

#### SIP Calculator Form

```typescript
// app/calculators/sip/components/SIPForm.tsx
'use client';

import { useState } from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';
import { Slider } from '@/components/ui/slider';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';
import { calculateSIP } from '@/lib/calculators/sip';
import { motion } from 'framer-motion';

const sipSchema = z.object({
  monthlyInvestment: z.number().min(500).max(1000000),
  expectedReturn: z.number().min(1).max(30),
  timePeriod: z.number().min(1).max(40),
});

type SIPFormData = z.infer<typeof sipSchema>;

export function SIPForm() {
  const [result, setResult] = useState(null);
  const [isCalculating, setIsCalculating] = useState(false);

  const {
    register,
    handleSubmit,
    watch,
    formState: { errors },
  } = useForm<SIPFormData>({
    resolver: zodResolver(sipSchema),
    defaultValues: {
      monthlyInvestment: 5000,
      expectedReturn: 12,
      timePeriod: 10,
    },
  });

  const values = watch();

  const onSubmit = async (data: SIPFormData) => {
    setIsCalculating(true);
    
    // Client-side calculation
    const result = calculateSIP(
      data.monthlyInvestment,
      data.expectedReturn,
      data.timePeriod
    );
    
    setResult(result);
    
    // Log usage
    await fetch('/api/log', {
      method: 'POST',
      body: JSON.stringify({
        type: 'sip',
        input: data,
        result,
      }),
    });
    
    setIsCalculating(false);
  };

  return (
    <Card className="p-6 bg-gradient-to-br from-background to-secondary/5 backdrop-blur">
      <form onSubmit={handleSubmit(onSubmit)} className="space-y-8">
        {/* Monthly Investment */}
        <div className="space-y-4">
          <div className="flex items-center justify-between">
            <label className="text-sm font-medium">
              Monthly Investment
            </label>
            <Input
              type="number"
              {...register('monthlyInvestment', { valueAsNumber: true })}
              className="w-32 text-right"
              prefix="₹"
            />
          </div>
          <Slider
            value={[values.monthlyInvestment]}
            onValueChange={([value]) =>
              register('monthlyInvestment').onChange({ target: { value } })
            }
            min={500}
            max={100000}
            step={500}
            className="w-full"
          />
          <div className="flex justify-between text-xs text-muted-foreground">
            <span>₹500</span>
            <span>₹1,00,000</span>
          </div>
        </div>

        {/* Expected Return */}
        <div className="space-y-4">
          <div className="flex items-center justify-between">
            <label className="text-sm font-medium">
              Expected Return (p.a.)
            </label>
            <Input
              type="number"
              {...register('expectedReturn', { valueAsNumber: true })}
              className="w-32 text-right"
              suffix="%"
            />
          </div>
          <Slider
            value={[values.expectedReturn]}
            onValueChange={([value]) =>
              register('expectedReturn').onChange({ target: { value } })
            }
            min={1}
            max={30}
            step={0.5}
            className="w-full"
          />
        </div>

        {/* Time Period */}
        <div className="space-y-4">
          <div className="flex items-center justify-between">
            <label className="text-sm font-medium">
              Time Period
            </label>
            <Input
              type="number"
              {...register('timePeriod', { valueAsNumber: true })}
              className="w-32 text-right"
              suffix="years"
            />
          </div>
          <Slider
            value={[values.timePeriod]}
            onValueChange={([value]) =>
              register('timePeriod').onChange({ target: { value } })
            }
            min={1}
            max={40}
            step={1}
            className="w-full"
          />
        </div>

        <Button
          type="submit"
          className="w-full"
          size="lg"
          disabled={isCalculating}
        >
          {isCalculating ? (
            <>
              <Loader2 className="mr-2 h-4 w-4 animate-spin" />
              Calculating...
            </>
          ) : (
            'Calculate Returns'
          )}
        </Button>
      </form>

      {result && (
        <motion.div
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.5 }}
          className="mt-8"
        >
          <SIPResults data={result} />
        </motion.div>
      )}
    </Card>
  );
}
```

---

## 6. Backend API

### 6.1 API Routes

```typescript
// app/api/calculate/sip/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { calculateSIP } from '@/lib/calculators/sip';
import { rateLimit } from '@/lib/rate-limit';

const sipSchema = z.object({
  monthlyInvestment: z.number().min(500).max(1000000),
  expectedReturn: z.number().min(1).max(30),
  timePeriod: z.number().min(1).max(40),
});

export async function POST(req: NextRequest) {
  try {
    // Rate limiting
    const identifier = req.ip ?? 'anonymous';
    const { success } = await rateLimit.limit(identifier);
    
    if (!success) {
      return NextResponse.json(
        { error: 'Too many requests' },
        { status: 429 }
      );
    }

    // Validate request body
    const body = await req.json();
    const data = sipSchema.parse(body);

    // Calculate
    const result = calculateSIP(
      data.monthlyInvestment,
      data.expectedReturn,
      data.timePeriod
    );

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

### 6.2 Calculator Logic

```typescript
// lib/calculators/sip.ts
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
  yearlyBreakdown: {
    year: number;
    invested: number;
    returns: number;
    total: number;
  }[];
}

export function calculateSIP(
  monthlyInvestment: number,
  expectedReturn: number,
  timePeriod: number
): SIPResult {
  const r = (expectedReturn / 12) / 100;
  const n = timePeriod * 12;

  // Future Value formula
  const FV = monthlyInvestment * (((Math.pow(1 + r, n) - 1) / r) * (1 + r));
  const totalInvested = monthlyInvestment * n;
  const estimatedReturns = FV - totalInvested;
  const wealthGain = (estimatedReturns / totalInvested) * 100;

  // Yearly breakdown
  const yearlyBreakdown = [];
  for (let year = 1; year <= timePeriod; year++) {
    const months = year * 12;
    const invested = monthlyInvestment * months;
    const fv = monthlyInvestment * (((Math.pow(1 + r, months) - 1) / r) * (1 + r));
    const returns = fv - invested;

    yearlyBreakdown.push({
      year,
      invested: Math.round(invested),
      returns: Math.round(returns),
      total: Math.round(fv),
    });
  }

  return {
    maturityAmount: Math.round(FV),
    totalInvested: Math.round(totalInvested),
    estimatedReturns: Math.round(estimatedReturns),
    wealthGain: parseFloat(wealthGain.toFixed(2)),
    yearlyBreakdown,
  };
}
```

---

## 7. UI/UX Design System

### 7.1 Color Palette

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss';

const config: Config = {
  darkMode: 'class',
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        secondary: {
          DEFAULT: 'hsl(var(--secondary))',
          foreground: 'hsl(var(--secondary-foreground))',
        },
        success: {
          DEFAULT: 'hsl(142, 76%, 36%)',
          foreground: 'hsl(0, 0%, 100%)',
        },
        destructive: {
          DEFAULT: 'hsl(var(--destructive))',
          foreground: 'hsl(var(--destructive-foreground))',
        },
        muted: {
          DEFAULT: 'hsl(var(--muted))',
          foreground: 'hsl(var(--muted-foreground))',
        },
        accent: {
          DEFAULT: 'hsl(var(--accent))',
          foreground: 'hsl(var(--accent-foreground))',
        },
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
      fontFamily: {
        sans: ['var(--font-inter)'],
        mono: ['var(--font-mono)'],
      },
      animation: {
        'fade-in': 'fade-in 0.5s ease-out',
        'slide-up': 'slide-up 0.5s ease-out',
        'slide-down': 'slide-down 0.5s ease-out',
      },
      keyframes: {
        'fade-in': {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        'slide-up': {
          '0%': { transform: 'translateY(20px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
        'slide-down': {
          '0%': { transform: 'translateY(-20px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
    },
  },
  plugins: [
    require('tailwindcss-animate'),
    require('@tailwindcss/typography'),
  ],
};

export default config;
```

### 7.2 Typography

```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --font-inter: 'Inter', system-ui, sans-serif;
    --font-mono: 'JetBrains Mono', monospace;
    
    /* Light mode */
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    
    /* ... other colors */
    
    --radius: 0.5rem;
  }

  .dark {
    /* Dark mode */
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    
    /* ... other colors */
  }
}

@layer base {
  * {
    @apply border-border;
  }
  
  body {
    @apply bg-background text-foreground font-sans;
    font-feature-settings: 'rlig' 1, 'calt' 1;
  }
  
  h1, h2, h3, h4, h5, h6 {
    @apply font-semibold tracking-tight;
  }
  
  h1 {
    @apply text-4xl md:text-5xl lg:text-6xl;
  }
  
  h2 {
    @apply text-3xl md:text-4xl lg:text-5xl;
  }
}
```

---

## 8. Performance Strategy

### 8.1 Performance Targets

```yaml
Lighthouse Scores (Target):
  Performance: 95+
  Accessibility: 100
  Best Practices: 100
  SEO: 100

Core Web Vitals:
  LCP (Largest Contentful Paint): < 1.5s
  FID (First Input Delay): < 50ms
  CLS (Cumulative Layout Shift): < 0.05

Page Load:
  First Paint: < 500ms
  Time to Interactive: < 2s
  Total Page Weight: < 500KB (gzipped)
```

### 8.2 Optimization Techniques

```typescript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Output standalone for Docker
  output: 'standalone',
  
  // Image optimization
  images: {
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 60 * 60 * 24 * 365, // 1 year
  },
  
  // Compression
  compress: true,
  
  // React compiler (experimental)
  experimental: {
    reactCompiler: true,
    ppr: true, // Partial Prerendering
  },
  
  // Bundle analyzer
  webpack: (config, { isServer }) => {
    if (!isServer) {
      // Bundle size optimization
      config.optimization.splitChunks = {
        chunks: 'all',
        cacheGroups: {
          default: false,
          vendors: false,
          commons: {
            name: 'commons',
            minChunks: 2,
            priority: 20,
          },
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendor',
            priority: 10,
            reuseExistingChunk: true,
          },
        },
      };
    }
    return config;
  },
};

module.exports = nextConfig;
```

---

## 9. Deployment Setup

### 9.1 Portainer Deployment Steps

```bash
# 1. SSH into your local server
ssh user@your-server-ip

# 2. Install Docker & Docker Compose
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# 3. Install Portainer
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest

# 4. Access Portainer
# Open browser: http://your-server-ip:9000
# Create admin account

# 5. Clone repository
git clone https://github.com/sumrendra/finance-calculator-india.git
cd finance-calculator-india

# 6. Create .env file
cp .env.example .env.production
nano .env.production
# Add your environment variables

# 7. Deploy via Portainer Web UI
# Stacks → Add Stack → Upload docker-compose.yml
# Or use Portainer git integration

# 8. Start containers
docker-compose up -d

# 9. Check logs
docker-compose logs -f web

# 10. Setup SSL (Let's Encrypt)
docker run -it --rm \
  -v /etc/letsencrypt:/etc/letsencrypt \
  -v /var/www/certbot:/var/www/certbot \
  certbot/certbot certonly --webroot \
  -w /var/www/certbot \
  -d financecalc.in \
  -d www.financecalc.in \
  --email your@email.com \
  --agree-tos --no-eff-email
```

### 9.2 Environment Variables

```bash
# .env.production
NODE_ENV=production

# Database
DATABASE_URL="postgresql://postgres:strongpassword@db:5432/financecalc?schema=public"
DB_PASSWORD=strongpassword

# Redis
REDIS_URL="redis://:redispassword@cache:6379"
REDIS_PASSWORD=redispassword

# NextAuth.js
NEXTAUTH_SECRET=your-super-secret-key-change-this
NEXTAUTH_URL=https://financecalc.in

# Google Analytics
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX

# Google AdSense
NEXT_PUBLIC_ADSENSE_ID=ca-pub-XXXXXXXXXX

# Cloudflare
CLOUDFLARE_API_TOKEN=your-cloudflare-token
```

### 9.3 Backup Strategy

```bash
# Automated backup script (backup.sh)
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"

# Backup PostgreSQL
docker exec finance-calculator-db pg_dump -U postgres financecalc | \
  gzip > $BACKUP_DIR/db_$DATE.sql.gz

# Backup uploaded files (if any)
tar -czf $BACKUP_DIR/files_$DATE.tar.gz /data/uploads

# Backup to S3 (optional)
aws s3 sync $BACKUP_DIR s3://your-backup-bucket/finance-calculator/

# Keep only last 30 days
find $BACKUP_DIR -type f -mtime +30 -delete

# Add to crontab
# 0 2 * * * /path/to/backup.sh
```

---

## 10. Development Workflow

### 10.1 Local Development

```bash
# Clone repository
git clone https://github.com/sumrendra/finance-calculator-india.git
cd finance-calculator-india/app

# Install dependencies
npm install

# Setup database (local Postgres or Docker)
docker-compose -f docker-compose.dev.yml up -d db cache

# Run Prisma migrations
npx prisma migrate dev

# Start development server
npm run dev

# Open browser
# http://localhost:3000

# Prisma Studio (database GUI)
npx prisma studio
```

### 10.2 Git Workflow

```yaml
Branches:
  main: Production-ready code
  develop: Development branch
  feature/*: New features
  fix/*: Bug fixes

Workflow:
  1. Create feature branch from develop
  2. Make changes
  3. Run tests: npm test
  4. Run linting: npm run lint
  5. Build: npm run build
  6. Create PR to develop
  7. Review & merge
  8. Deploy to staging
  9. Test
  10. Merge develop to main
  11. Deploy to production (Portainer)
```

### 10.3 CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build
      
      - name: Deploy to Server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /home/user/finance-calculator-india
            git pull origin main
            docker-compose down
            docker-compose up -d --build
```

---

## 11. Future Cloud Migration Options

### When to Migrate (Phase 2+)

```yaml
Triggers:
  - Traffic > 50K pageviews/month
  - Need better uptime guarantee
  - Scaling issues on local server
  - Want global CDN
  - Need managed database backups

Best Options:

1. Vercel (Best for Next.js)
   Pros:
     ✓ Zero configuration
     ✓ Automatic deployments
     ✓ Global CDN
     ✓ Serverless functions
     ✓ Built-in analytics
   Cons:
     ✗ More expensive ($20-$40/mo)
     ✗ Vendor lock-in
   Cost: $20/mo (Hobby Pro) or $40/mo (Pro)

2. Railway
   Pros:
     ✓ Easy to use
     ✓ GitHub integration
     ✓ PostgreSQL included
     ✓ Good pricing
   Cons:
     ✗ Newer platform
   Cost: $5-20/mo (usage-based)

3. DigitalOcean App Platform
   Pros:
     ✓ Simple deployment
     ✓ Managed database option
     ✓ Good documentation
   Cons:
     ✗ Less features than Vercel
   Cost: $12/mo (App Platform) + $15/mo (Database)

4. AWS Lightsail Containers
   Pros:
     ✓ Very cheap
     ✓ AWS ecosystem
     ✓ Predictable pricing
   Cons:
     ✗ More complex setup
   Cost: $7-15/mo

Recommendation: Start with Vercel when traffic justifies the cost
```

---

## Appendix A: Package.json

```json
{
  "name": "finance-calculator",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "format": "prettier --write \"**/*.{ts,tsx,md}\"",
    "test": "jest",
    "test:watch": "jest --watch",
    "db:push": "prisma db push",
    "db:migrate": "prisma migrate dev",
    "db:studio": "prisma studio",
    "db:seed": "tsx prisma/seed.ts"
  },
  "dependencies": {
    "next": "^14.1.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "@prisma/client": "^5.9.0",
    "@radix-ui/react-slider": "^1.1.2",
    "@radix-ui/react-toast": "^1.1.5",
    "@radix-ui/react-dialog": "^1.0.5",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.1.0",
    "framer-motion": "^11.0.0",
    "react-hook-form": "^7.49.3",
    "@hookform/resolvers": "^3.3.4",
    "zod": "^3.22.4",
    "recharts": "^2.11.0",
    "tailwind-merge": "^2.2.0",
    "tailwindcss-animate": "^1.0.7",
    "ioredis": "^5.3.2",
    "next-themes": "^0.2.1",
    "lucide-react": "^0.312.0"
  },
  "devDependencies": {
    "@types/node": "^20.11.0",
    "@types/react": "^18.2.48",
    "@types/react-dom": "^18.2.18",
    "typescript": "^5.3.3",
    "prisma": "^5.9.0",
    "tailwindcss": "^3.4.1",
    "postcss": "^8.4.33",
    "autoprefixer": "^10.4.17",
    "@tailwindcss/typography": "^0.5.10",
    "prettier": "^3.2.4",
    "prettier-plugin-tailwindcss": "^0.5.11",
    "eslint": "^8.56.0",
    "eslint-config-next": "^14.1.0",
    "@testing-library/react": "^14.1.2",
    "@testing-library/jest-dom": "^6.2.0",
    "jest": "^29.7.0",
    "jest-environment-jsdom": "^29.7.0"
  }
}
```

---

## Appendix B: Quick Start Checklist

```yaml
Phase 1: Local Setup (Week 1)
  ☐ Install Docker & Docker Compose
  ☐ Install Portainer
  ☐ Clone repository
  ☐ Configure environment variables
  ☐ Start containers via Portainer
  ☐ Run database migrations
  ☐ Test locally (http://localhost:3000)
  ☐ Setup Cloudflare DNS
  ☐ Configure SSL certificates
  ☐ Deploy to local server
  ☐ Test production (https://financecalc.in)

Phase 2: Development (Week 2-4)
  ☐ Build homepage with beautiful hero section
  ☐ Implement SIP calculator with animations
  ☐ Implement EMI calculator with charts
  ☐ Implement FD calculator with comparisons
  ☐ Add dark mode toggle
  ☐ Optimize images & fonts
  ☐ Add Google Analytics
  ☐ Add AdSense ads
  ☐ Write first 5 blog posts
  ☐ SEO optimization

Phase 3: Polish & Launch (Week 5-6)
  ☐ Cross-browser testing
  ☐ Mobile responsiveness
  ☐ Performance optimization (Lighthouse 95+)
  ☐ Accessibility audit
  ☐ Security audit
  ☐ Backup system setup
  ☐ Monitoring setup
  ☐ Soft launch
  ☐ Collect feedback
  ☐ Official launch 🚀
```

---

**End of Technical Specification Document v2.0**

**This modern architecture provides:**
- ⚡ Blazing fast performance (<1s loads)
- 🎨 Beautiful, modern UI (shadcn/ui components)
- 🐳 Easy containerized deployment (Portainer)
- 📱 Mobile-first responsive design
- 🔒 Production-ready security
- 📊 Real-time analytics
- 🚀 Scalable to cloud when needed

**Ready to build something amazing! Let's start with Phase 1.**
