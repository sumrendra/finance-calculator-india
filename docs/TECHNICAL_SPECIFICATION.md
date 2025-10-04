# Technical Specification Document
## Finance Calculator Hub - Indian Market

**Document Version:** 1.0  
**Last Updated:** October 4, 2025  
**Status:** Approved for Development  
**Classification:** Internal Use

---

## Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Oct 4, 2025 | Development Team | Initial technical specification |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [System Architecture](#2-system-architecture)
3. [Technology Stack](#3-technology-stack)
4. [Database Design](#4-database-design)
5. [Application Components](#5-application-components)
6. [Security Architecture](#6-security-architecture)
7. [Performance Requirements](#7-performance-requirements)
8. [API Specifications](#8-api-specifications)
9. [Deployment Architecture](#9-deployment-architecture)
10. [Monitoring & Observability](#10-monitoring--observability)
11. [Development Workflow](#11-development-workflow)
12. [Testing Strategy](#12-testing-strategy)

---

## 1. Executive Summary

### 1.1 Project Overview

**Project Name:** Finance Calculator Hub  
**Target Market:** India  
**Primary Objective:** Create a high-performance financial calculator platform monetized through AdSense  
**Expected Traffic:** 150K+ monthly pageviews by Month 12  
**Revenue Target:** ₹25-40K/month by Month 12

### 1.2 Key Technical Goals

- **Page Load Time:** < 2 seconds (mobile & desktop)
- **Uptime:** 99.9% availability
- **Scalability:** Support 500K+ pageviews/month
- **SEO Performance:** PageSpeed score 85+ (mobile), 95+ (desktop)
- **Mobile-First:** Optimized for 70% mobile traffic

### 1.3 Technology Decisions

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| CMS | WordPress 6.4+ | Fast development, rich plugin ecosystem, excellent SEO |
| Language | PHP 8.2+ | WordPress requirement, modern features |
| Database | MySQL 8.0+ | WordPress standard, managed hosting support |
| Frontend | JavaScript ES6+ | Real-time calculations, no framework overhead |
| Caching | Redis | High performance, object caching |
| CDN | Cloudflare | Global distribution, security, free tier |
| Hosting | Cloudways/WP Engine | Managed WordPress, optimization included |

---

## 2. System Architecture

### 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Browsers: Chrome, Safari, Firefox, Edge                 │  │
│  │  Devices: Mobile (70%), Desktop (25%), Tablet (5%)       │  │
│  │  Locations: India (Tier 1, 2, 3 cities)                 │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓ HTTPS
┌─────────────────────────────────────────────────────────────────┐
│                         EDGE LAYER                               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              Cloudflare CDN + Security                   │  │
│  │  • Global edge caching (30+ locations)                   │  │
│  │  • DDoS protection (automatic)                           │  │
│  │  • Web Application Firewall (WAF)                        │  │
│  │  • SSL/TLS termination                                   │  │
│  │  • Brotli/Gzip compression                               │  │
│  │  • Auto-minification (JS, CSS, HTML)                     │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              WordPress Application Server                 │  │
│  │                                                           │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │  │
│  │  │   Apache/   │  │  WordPress  │  │   Custom    │    │  │
│  │  │   Nginx     │→ │   Core      │→ │   Plugins   │    │  │
│  │  │  (HTTP/2)   │  │   PHP-FPM   │  │  & Themes   │    │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘    │  │
│  │                                                           │  │
│  │  Server Specs (Phase 1):                                 │  │
│  │  • 2 vCPUs, 4GB RAM, 80GB SSD                           │  │
│  │  • Ubuntu 22.04 LTS or managed platform                 │  │
│  │  • PHP 8.2+ with OPcache enabled                        │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      CACHING LAYER                               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  ┌───────────────┐  ┌──────────────┐  ┌─────────────┐  │  │
│  │  │  Page Cache   │  │ Object Cache │  │ Opcode Cache│  │  │
│  │  │  (W3 Total /  │  │   (Redis)    │  │  (OPcache)  │  │  │
│  │  │  WP Rocket)   │  │              │  │             │  │  │
│  │  └───────────────┘  └──────────────┘  └─────────────┘  │  │
│  │                                                           │  │
│  │  Cache Hierarchy:                                        │  │
│  │  1. Browser cache (static assets: 1 year)               │  │
│  │  2. CDN cache (pages: 1 hour, assets: 1 week)          │  │
│  │  3. Page cache (full HTML: 24 hours)                    │  │
│  │  4. Object cache (DB queries: 5 mins - 1 hour)         │  │
│  │  5. OPcache (compiled PHP: until deployment)            │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                       DATA LAYER                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              MySQL 8.0+ Database                         │  │
│  │                                                           │  │
│  │  Primary Database:                                       │  │
│  │  • WordPress core tables (wp_posts, wp_users, etc.)     │  │
│  │  • Custom calculator tables (usage logs, saved calcs)   │  │
│  │  • Interest rate cache table                            │  │
│  │                                                           │  │
│  │  Configuration:                                          │  │
│  │  • InnoDB storage engine                                │  │
│  │  • utf8mb4 character set                                │  │
│  │  • Automated daily backups (30-day retention)           │  │
│  │  • Point-in-time recovery capability                    │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  • Google Analytics 4 (user behavior tracking)           │  │
│  │  • Google Search Console (SEO monitoring)                │  │
│  │  • Google AdSense (monetization)                         │  │
│  │  • Cloudflare Analytics (performance metrics)            │  │
│  │  • GitHub (version control & CI/CD)                      │  │
│  │  • Uptime Robot (availability monitoring)                │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 Request Flow

#### 2.2.1 First-Time Visitor

```
User Request → Cloudflare CDN (MISS) → WordPress Server →
Calculate Response → Cache at all levels → Serve to user
Total Time: ~1.5-2.5 seconds
```

#### 2.2.2 Returning Visitor (Cached)

```
User Request → Cloudflare CDN (HIT) → Serve from edge
Total Time: ~300-500ms
```

#### 2.2.3 Calculator Interaction

```
User Input → JavaScript Validation → Client-side Calculation →
Display Results → (Optional) API call to log usage →
Update Analytics
Total Time: <100ms for calculation
```

### 2.3 Component Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                        │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐  │
│  │  HTML Pages  │  │   CSS Files  │  │  JavaScript │  │
│  │  (WordPress  │  │  (Tailwind)  │  │  (Vanilla)  │  │
│  │   Template)  │  │              │  │             │  │
│  └──────────────┘  └──────────────┘  └─────────────┘  │
│         │                  │                  │         │
│         └──────────────────┴──────────────────┘         │
│                           │                              │
│                  ┌────────▼────────┐                    │
│                  │  WordPress Theme │                    │
│                  │  (Astra/GP Pro)  │                    │
│                  └─────────────────┘                    │
└─────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│                    PLUGIN LAYER                          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────┐  ┌───────────────────────────┐  │
│  │  Calculator      │  │  SEO Plugin               │  │
│  │  Plugin (Custom) │  │  (Rank Math/Yoast)        │  │
│  └──────────────────┘  └───────────────────────────┘  │
│                                                          │
│  ┌──────────────────┐  ┌───────────────────────────┐  │
│  │  Caching Plugin  │  │  Security Plugin          │  │
│  │  (W3TC/WPRocket) │  │  (Wordfence/Sucuri)       │  │
│  └──────────────────┘  └───────────────────────────┘  │
│                                                          │
│  ┌──────────────────┐  ┌───────────────────────────┐  │
│  │  AdSense Plugin  │  │  Analytics Plugin         │  │
│  │  (Advanced Ads)  │  │  (MonsterInsights)        │  │
│  └──────────────────┘  └───────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│                    WORDPRESS CORE                        │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  • Post/Page Management                                 │
│  • User Authentication & Authorization                  │
│  • Media Library                                        │
│  • REST API                                             │
│  • Hook System (actions/filters)                        │
│  • Database Abstraction Layer (wpdb)                    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## 3. Technology Stack

### 3.1 Core Technologies

#### 3.1.1 Backend Stack

```yaml
Platform:
  CMS: WordPress 6.4+
  PHP Version: 8.2+
  Web Server: Nginx 1.24+ OR Apache 2.4+
  Database: MySQL 8.0+ OR MariaDB 10.11+
  
PHP Extensions (Required):
  - php-mbstring (multibyte string handling)
  - php-curl (API calls)
  - php-xml (XML processing)
  - php-zip (plugin/theme installation)
  - php-gd or php-imagick (image processing)
  - php-intl (internationalization)
  - php-redis (object caching)
  - php-opcache (opcode caching)
  
WordPress Configuration:
  WP_DEBUG: false (production)
  WP_DEBUG_LOG: true (log to file)
  WP_CACHE: true
  WP_MEMORY_LIMIT: 256M
  WP_MAX_MEMORY_LIMIT: 512M
  DISALLOW_FILE_EDIT: true (security)
  AUTOMATIC_UPDATER_DISABLED: true (manual updates)
```

#### 3.1.2 Frontend Stack

```yaml
HTML/CSS:
  HTML Version: HTML5
  CSS Framework: Tailwind CSS 3.x
  CSS Preprocessor: PostCSS
  Responsive Breakpoints:
    - Mobile: < 768px (primary focus)
    - Tablet: 768px - 1023px
    - Desktop: ≥ 1024px
  
JavaScript:
  ECMAScript: ES6+ (transpiled to ES5 for compatibility)
  No Framework: Vanilla JavaScript for performance
  Libraries:
    - Chart.js 4.x (data visualization)
    - Day.js (date manipulation, lighter than Moment.js)
  Build Tool: Webpack 5 OR Vite 4
  
Fonts:
  Primary: System fonts for performance
  Fallback: Google Fonts (Roboto/Inter)
  Loading: font-display: swap
  Subset: Latin only
```

#### 3.1.3 Database Schema

```sql
-- Character Set Configuration
SET NAMES utf8mb4;
SET CHARACTER SET utf8mb4;
COLLATE utf8mb4_unicode_ci;

-- Calculator Usage Logging Table
CREATE TABLE fc_calculator_logs (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    calculator_type VARCHAR(50) NOT NULL,
    input_data JSON NOT NULL,
    result_data JSON,
    user_ip VARCHAR(45),
    user_agent TEXT,
    session_id VARCHAR(64),
    page_url VARCHAR(500),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_calculator_type (calculator_type),
    INDEX idx_created_at (created_at),
    INDEX idx_session_id (session_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Saved Calculations (Phase 3 - User Accounts)
CREATE TABLE fc_saved_calculations (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    calculator_type VARCHAR(50) NOT NULL,
    title VARCHAR(255),
    input_data JSON NOT NULL,
    result_data JSON NOT NULL,
    is_private BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES wp_users(ID) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_calculator_type (calculator_type),
    INDEX idx_created_at (created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Interest Rate Cache (Phase 2)
CREATE TABLE fc_interest_rates (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    bank_name VARCHAR(100) NOT NULL,
    product_type VARCHAR(50) NOT NULL COMMENT 'FD, RD, Home_Loan, Car_Loan, Personal_Loan',
    rate_type VARCHAR(20) NOT NULL COMMENT 'Fixed, Floating, Variable',
    tenure_months INT COMMENT 'Loan/deposit tenure in months',
    interest_rate DECIMAL(5,2) NOT NULL COMMENT 'Annual interest rate',
    effective_from DATE NOT NULL,
    effective_to DATE,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    source_url TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    
    UNIQUE KEY unique_rate (bank_name, product_type, rate_type, tenure_months, effective_from),
    INDEX idx_product_type (product_type),
    INDEX idx_bank_name (bank_name),
    INDEX idx_is_active (is_active),
    INDEX idx_effective_from (effective_from)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Content Performance Metrics (Analytics)
CREATE TABLE fc_content_metrics (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    post_id BIGINT UNSIGNED NOT NULL,
    metric_date DATE NOT NULL,
    pageviews INT DEFAULT 0,
    unique_visitors INT DEFAULT 0,
    avg_time_on_page INT DEFAULT 0 COMMENT 'Seconds',
    bounce_rate DECIMAL(5,2) DEFAULT 0.00,
    calculator_interactions INT DEFAULT 0,
    adsense_impressions INT DEFAULT 0,
    adsense_clicks INT DEFAULT 0,
    revenue_generated DECIMAL(10,2) DEFAULT 0.00 COMMENT 'In INR',
    
    FOREIGN KEY (post_id) REFERENCES wp_posts(ID) ON DELETE CASCADE,
    UNIQUE KEY unique_daily_metric (post_id, metric_date),
    INDEX idx_metric_date (metric_date),
    INDEX idx_revenue (revenue_generated)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- AB Test Tracking (Phase 3)
CREATE TABLE fc_ab_tests (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    test_name VARCHAR(100) NOT NULL,
    variant VARCHAR(50) NOT NULL COMMENT 'A, B, C, etc.',
    page_url VARCHAR(500),
    conversions INT DEFAULT 0,
    impressions INT DEFAULT 0,
    conversion_rate DECIMAL(5,2) GENERATED ALWAYS AS (
        CASE WHEN impressions > 0 
        THEN (conversions / impressions) * 100 
        ELSE 0 END
    ) STORED,
    started_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ended_at TIMESTAMP NULL,
    is_active BOOLEAN DEFAULT TRUE,
    
    INDEX idx_test_name (test_name),
    INDEX idx_is_active (is_active)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### 3.2 WordPress Plugins

#### 3.2.1 Essential Plugins (Phase 1)

```yaml
SEO:
  Plugin: Rank Math Pro OR Yoast SEO Premium
  Purpose: On-page SEO, schema markup, sitemap generation
  Version: Latest stable
  License: $59-$99/year
  
Performance:
  Plugin: WP Rocket OR W3 Total Cache
  Purpose: Page caching, minification, CDN integration
  Version: Latest stable
  License: $49/year (WP Rocket) or Free (W3TC)
  Features:
    - Page caching
    - Browser caching
    - GZIP compression
    - Minification (HTML, CSS, JS)
    - Lazy loading images
    - Database optimization
  
Security:
  Plugin: Wordfence Security OR Sucuri Security
  Purpose: Firewall, malware scanning, brute force protection
  Version: Latest stable
  License: Free (basic) or $99/year (premium)
  Features:
    - Web Application Firewall (WAF)
    - Malware scanner
    - Login security (2FA, limit attempts)
    - Real-time threat defense
  
Image Optimization:
  Plugin: EWWW Image Optimizer OR ShortPixel
  Purpose: Automatic image compression, WebP conversion
  Version: Latest stable
  License: Free (limited) or $7/month
```

#### 3.2.2 Custom Calculator Plugin

```yaml
Plugin Name: FC Calculator Suite
Description: Custom financial calculators for Indian market
Version: 1.0.0
Author: Development Team
License: Proprietary

Features:
  - Multiple calculator types (SIP, EMI, FD, RD, PPF, ELSS, Tax)
  - Shortcode system for easy embedding
  - REST API endpoints for AJAX calculations
  - Usage analytics tracking
  - Admin dashboard for calculator management
  - Export/import calculator configurations

File Structure:
  fc-calculator-suite/
  ├── fc-calculator-suite.php (main plugin file)
  ├── includes/
  │   ├── class-calculator-base.php
  │   ├── class-sip-calculator.php
  │   ├── class-emi-calculator.php
  │   ├── class-fd-calculator.php
  │   └── class-calculator-api.php
  ├── assets/
  │   ├── css/
  │   │   └── calculator.css
  │   └── js/
  │       ├── calculator-base.js
  │       └── chart-utils.js
  ├── templates/
  │   ├── calculator-sip.php
  │   ├── calculator-emi.php
  │   └── calculator-results.php
  └── admin/
      ├── class-calculator-admin.php
      └── views/
          └── dashboard.php

Shortcode Examples:
  [fc_calculator type="sip"]
  [fc_calculator type="emi" loan_type="home"]
  [fc_calculator type="fd" bank="sbi"]
```

### 3.3 Hosting Infrastructure

#### 3.3.1 Hosting Options

**Option 1: Cloudways (Recommended for Phase 1-2)**

```yaml
Provider: Cloudways (DigitalOcean backend)
Plan: DO Basic (Phase 1) → DO Premium (Phase 2+)

Phase 1 Specs (0-50K pageviews/month):
  CPU: 2 vCPUs
  RAM: 4GB
  Storage: 80GB SSD
  Bandwidth: 4TB/month
  Cost: $24/month
  
Included Features:
  - Managed WordPress
  - Built-in Redis (object caching)
  - Free SSL certificate (Let's Encrypt)
  - Staging environment
  - Automated backups (7 days retention)
  - Built-in CDN (Cloudflare integration)
  - Git integration
  - SSH/SFTP access
  - WordPress management tools
  - 24/7 support

Scaling Path:
  50K-150K pageviews: Upgrade to 4 vCPU, 8GB RAM ($48/month)
  150K-500K pageviews: Premium plan with load balancer ($96/month)
  500K+ pageviews: Custom enterprise setup
```

**Option 2: WP Engine (Alternative - Premium)**

```yaml
Provider: WP Engine
Plan: Startup (Phase 1) → Growth (Phase 2+)

Phase 1 Specs:
  Storage: 10GB
  Bandwidth: 50GB/month
  Sites: 1
  Visits: 25K/month
  Cost: $30/month (annual) or $35/month (monthly)
  
Included Features:
  - Fully managed WordPress
  - Automatic updates
  - Daily automated backups
  - Free SSL
  - Global CDN (powered by MaxCDN)
  - Staging environment
  - Page performance monitoring
  - Threat detection & blocking
  - 24/7 support

Advantages:
  - Best WordPress performance (Genesis Framework optimized)
  - Excellent support
  - Built-in performance tools
  
Disadvantages:
  - Higher cost per visit
  - Visit-based pricing (can be limiting)
  - Less control than Cloudways
```

#### 3.3.2 CDN Configuration

```yaml
Provider: Cloudflare
Plan: Free (Phase 1) → Pro ($20/month, Phase 2+)

DNS Configuration:
  - Nameservers: Point domain to Cloudflare
  - Proxy status: Enabled (orange cloud)
  - SSL/TLS: Full (strict)
  
Page Rules (Free plan: 3 rules):
  1. Cache Everything:
     URL: financecalc.in/*
     Cache Level: Cache Everything
     Edge Cache TTL: 1 hour
  
  2. Bypass Cache for Dynamic Content:
     URL: financecalc.in/wp-admin/*
     Cache Level: Bypass
  
  3. Bypass Cache for API:
     URL: financecalc.in/wp-json/*
     Cache Level: Bypass

Performance Settings:
  - Auto Minify: HTML, CSS, JavaScript (ON)
  - Brotli Compression: ON
  - Early Hints: ON
  - HTTP/2: ON
  - HTTP/3 (QUIC): ON
  - Rocket Loader: OFF (can break calculators)
  - Image Optimization: ON (Pro plan)
  
Security Settings:
  - Security Level: Medium
  - Challenge Passage: 30 minutes
  - Browser Integrity Check: ON
  - WAF: ON
  - DDoS Protection: Automatic
```

---

## 4. Database Design

### 4.1 WordPress Core Tables (Standard)

```
wp_posts          - Calculator pages, blog posts
wp_postmeta       - Custom fields for calculators
wp_users          - User accounts (Phase 3)
wp_usermeta       - User preferences
wp_options        - Site configuration
wp_terms          - Categories, tags
wp_term_taxonomy  - Taxonomy definitions
wp_term_relationships - Content relationships
wp_comments       - User comments (if enabled)
wp_commentmeta    - Comment metadata
```

### 4.2 Custom Tables

See section 3.1.3 for complete SQL schema.

### 4.3 Database Optimization

```sql
-- Indexes for Performance
-- WordPress core tables already have indexes, but verify:

-- Optimize wp_options table (often bloated)
ALTER TABLE wp_options ADD INDEX autoload_idx (autoload);

-- Optimize wp_postmeta for calculator queries
ALTER TABLE wp_postmeta ADD INDEX meta_key_value_idx (meta_key, meta_value(20));

-- Optimize wp_posts for calculator queries
ALTER TABLE wp_posts 
  ADD INDEX type_status_date_idx (post_type, post_status, post_date);

-- Database Maintenance Schedule
-- Run weekly via WP-CLI cron:
-- wp db optimize
-- wp transient delete --expired
-- wp cache flush
```

### 4.4 Data Retention Policy

```yaml
Calculator Usage Logs:
  Retention: 2 years
  Cleanup: Automated monthly job
  Archive: Export to CSV before deletion

Saved Calculations (User):
  Retention: Until user deletes or account closes
  Inactive Account: Delete after 1 year of inactivity
  
Interest Rate Data:
  Retention: 5 years (historical data)
  Archive: Keep forever (compressed)

Analytics Data:
  Retention: 3 years
  Aggregation: Daily → Monthly after 6 months
```

---

## 5. Application Components

### 5.1 Calculator Architecture

```javascript
/**
 * Base Calculator Class
 * All calculators extend this base class
 */
class FCCalculatorBase {
    constructor(containerId, calculatorType) {
        this.container = document.getElementById(containerId);
        this.type = calculatorType;
        this.inputs = {};
        this.results = {};
        this.validationRules = {};
        this.charts = {};
        
        this.init();
    }
    
    init() {
        this.renderUI();
        this.attachEventListeners();
        this.loadDefaults();
    }
    
    // Abstract methods - must be implemented by child classes
    calculate() {
        throw new Error('calculate() must be implemented');
    }
    
    displayResults() {
        throw new Error('displayResults() must be implemented');
    }
    
    // Common methods available to all calculators
    validate(field) {
        // Input validation logic
    }
    
    formatCurrency(amount) {
        return new Intl.NumberFormat('en-IN', {
            style: 'currency',
            currency: 'INR',
            maximumFractionDigits: 0
        }).format(amount);
    }
    
    trackUsage() {
        // Send to Google Analytics
        gtag('event', 'calculator_use', {
            'calculator_type': this.type,
            'event_category': 'Calculator',
            'event_label': this.type
        });
        
        // Send to custom API for logging
        fetch('/wp-json/fc/v1/log', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({
                type: this.type,
                inputs: this.inputs,
                results: this.results
            })
        });
    }
    
    share() {
        if (navigator.share) {
            navigator.share({
                title: `${this.type} Calculator Results`,
                text: this.getShareText(),
                url: window.location.href
            });
        } else {
            // Fallback: copy to clipboard
            this.copyToClipboard(window.location.href);
        }
    }
    
    print() {
        window.print();
    }
    
    reset() {
        this.inputs = {};
        this.results = {};
        this.clearCharts();
        this.renderUI();
    }
}

/**
 * SIP Calculator Implementation
 */
class FCCalculatorSIP extends FCCalculatorBase {
    constructor(containerId) {
        super(containerId, 'sip');
        
        this.validationRules = {
            monthlyInvestment: {
                min: 500,
                max: 1000000,
                step: 500
            },
            expectedReturn: {
                min: 1,
                max: 30,
                step: 0.5
            },
            timePeriod: {
                min: 1,
                max: 40,
                step: 1
            }
        };
    }
    
    calculate() {
        const P = parseFloat(this.inputs.monthlyInvestment);
        const annualRate = parseFloat(this.inputs.expectedReturn);
        const years = parseFloat(this.inputs.timePeriod);
        
        const r = (annualRate / 12) / 100;
        const n = years * 12;
        
        // Future Value formula
        const FV = P * (((Math.pow(1 + r, n) - 1) / r) * (1 + r));
        
        this.results = {
            maturityAmount: Math.round(FV),
            totalInvested: Math.round(P * n),
            estimatedReturns: Math.round(FV - (P * n)),
            wealthGain: (((FV - (P * n)) / (P * n)) * 100).toFixed(2)
        };
        
        this.displayResults();
        this.generateCharts();
        this.trackUsage();
    }
    
    displayResults() {
        // Render results HTML
    }
    
    generateCharts() {
        // Create pie chart and growth chart using Chart.js
    }
}
```

### 5.2 REST API Endpoints

```php
<?php
/**
 * Custom REST API for calculators
 * File: includes/class-calculator-api.php
 */

class FC_Calculator_API {
    
    public function __construct() {
        add_action('rest_api_init', array($this, 'register_routes'));
    }
    
    public function register_routes() {
        $namespace = 'fc/v1';
        
        // Calculator endpoints
        register_rest_route($namespace, '/calculate/sip', array(
            'methods' => 'POST',
            'callback' => array($this, 'calculate_sip'),
            'permission_callback' => '__return_true',
            'args' => $this->get_sip_args()
        ));
        
        register_rest_route($namespace, '/calculate/emi', array(
            'methods' => 'POST',
            'callback' => array($this, 'calculate_emi'),
            'permission_callback' => '__return_true',
            'args' => $this->get_emi_args()
        ));
        
        // Usage logging endpoint
        register_rest_route($namespace, '/log', array(
            'methods' => 'POST',
            'callback' => array($this, 'log_usage'),
            'permission_callback' => '__return_true'
        ));
        
        // Interest rates endpoint
        register_rest_route($namespace, '/rates', array(
            'methods' => 'GET',
            'callback' => array($this, 'get_interest_rates'),
            'permission_callback' => '__return_true',
            'args' => array(
                'bank' => array('required' => false),
                'product' => array('required' => false)
            )
        ));
    }
    
    public function calculate_sip($request) {
        $params = $request->get_params();
        
        $P = floatval($params['monthlyInvestment']);
        $r = (floatval($params['expectedReturn']) / 12) / 100;
        $n = floatval($params['timePeriod']) * 12;
        
        $FV = $P * (((pow(1 + $r, $n) - 1) / $r) * (1 + $r));
        $totalInvested = $P * $n;
        
        return new WP_REST_Response(array(
            'success' => true,
            'data' => array(
                'maturityAmount' => round($FV),
                'totalInvested' => round($totalInvested),
                'estimatedReturns' => round($FV - $totalInvested),
                'wealthGain' => round((($FV - $totalInvested) / $totalInvested) * 100, 2)
            )
        ), 200);
    }
    
    private function get_sip_args() {
        return array(
            'monthlyInvestment' => array(
                'required' => true,
                'type' => 'number',
                'minimum' => 500,
                'maximum' => 1000000
            ),
            'expectedReturn' => array(
                'required' => true,
                'type' => 'number',
                'minimum' => 1,
                'maximum' => 30
            ),
            'timePeriod' => array(
                'required' => true,
                'type' => 'integer',
                'minimum' => 1,
                'maximum' => 40
            )
        );
    }
}

new FC_Calculator_API();
```

### 5.3 Theme Structure

```
astra-child/
├── style.css (theme metadata)
├── functions.php (theme setup)
├── header.php
├── footer.php
├── index.php
├── single.php
├── page.php
├── page-calculator.php (custom template)
├── sidebar.php
├── assets/
│   ├── css/
│   │   ├── main.css
│   │   ├── calculator.css
│   │   └── responsive.css
│   ├── js/
│   │   ├── main.js
│   │   ├── calculators/
│   │   │   ├── sip.js
│   │   │   ├── emi.js
│   │   │   └── fd.js
│   │   └── vendor/
│   │       └── chart.min.js
│   └── images/
├── template-parts/
│   ├── calculator-header.php
│   ├── calculator-footer.php
│   └── related-calculators.php
└── inc/
    ├── customizer.php
    ├── template-functions.php
    └── enqueue-scripts.php
```

---

## 6. Security Architecture

### 6.1 Security Layers

```yaml
Layer 1: Network Security (Cloudflare)
  - DDoS mitigation (automatic)
  - Web Application Firewall (WAF)
  - SSL/TLS encryption (TLS 1.3)
  - IP reputation filtering
  - Rate limiting (100 requests/minute per IP)

Layer 2: Server Security (Hosting)
  - SSH key authentication only (no password)
  - Firewall (UFW) - only ports 80, 443, 22 open
  - Automatic security updates
  - Fail2ban for brute force protection
  - ModSecurity (if Apache)

Layer 3: Application Security (WordPress)
  - Strong password policy (min 12 chars, mixed case, numbers, symbols)
  - Two-factor authentication (2FA) for all admin users
  - Limited login attempts (3 tries, 15 min lockout)
  - File permissions (644 files, 755 directories)
  - Disable file editing from admin panel
  - XML-RPC disabled (unless needed)
  - Remove WordPress version info
  - Hide login URL (change from /wp-admin)

Layer 4: Data Security
  - Input sanitization (all user inputs)
  - Output escaping (prevent XSS)
  - Prepared statements (prevent SQL injection)
  - CSRF tokens on forms
  - IP address anonymization (GDPR)
  - Secure cookies (httponly, secure flags)
```

### 6.2 WordPress Hardening

```php
<?php
// wp-config.php security enhancements

// Disable file editing from admin panel
define('DISALLOW_FILE_EDIT', true);

// Limit post revisions
define('WP_POST_REVISIONS', 5);

// Increase autosave interval
define('AUTOSAVE_INTERVAL', 300); // 5 minutes

// Security keys (generate unique values from api.wordpress.org/secret-key/1.1/salt/)
define('AUTH_KEY',         'put-unique-phrase-here');
define('SECURE_AUTH_KEY',  'put-unique-phrase-here');
define('LOGGED_IN_KEY',    'put-unique-phrase-here');
define('NONCE_KEY',        'put-unique-phrase-here');
define('AUTH_SALT',        'put-unique-phrase-here');
define('SECURE_AUTH_SALT', 'put-unique-phrase-here');
define('LOGGED_IN_SALT',   'put-unique-phrase-here');
define('NONCE_SALT',       'put-unique-phrase-here');

// Change database table prefix (not wp_)
$table_prefix = 'fc_';

// Force SSL for admin and login
define('FORCE_SSL_ADMIN', true);

// Disable XML-RPC
add_filter('xmlrpc_enabled', '__return_false');

// Remove WordPress version
remove_action('wp_head', 'wp_generator');
```

### 6.3 Data Protection & Privacy

```yaml
GDPR Compliance:
  - Privacy Policy page (required)
  - Cookie consent banner
  - Data export capability (user request)
  - Data deletion capability (user request)
  - IP address anonymization in logs
  - No data sold to third parties
  
Cookie Policy:
  Essential Cookies:
    - PHPSESSID (session management)
    - wordpress_* (authentication)
    - wp-settings-* (user preferences)
  
  Analytics Cookies (optional, user consent):
    - _ga (Google Analytics)
    - _gid (Google Analytics)
    - _gat (Google Analytics)
  
  Data Collection:
    What: Calculator usage, page views, IP (anonymized)
    Why: Improve service, analytics
    Retention: 2 years max
    Third Parties: Google (Analytics, AdSense), Cloudflare
```

### 6.4 Backup Strategy

```yaml
Automated Backups:
  Frequency: Daily (3 AM IST)
  Retention: 30 days rolling
  Components:
    - Database (full backup)
    - wp-content/ directory
    - Custom plugins
    - Theme files
  Exclude:
    - wp-content/cache/
    - wp-content/uploads/ (backed up weekly)
  
  Storage Locations:
    - Primary: Hosting provider (7 days)
    - Secondary: AWS S3 / Backblaze B2 (30 days)
    - Tertiary: Local download (weekly, manual)
  
Manual Backups:
  - Before major updates
  - Before plugin installations
  - Before code deployments
  
Backup Testing:
  - Monthly restoration test on staging server
  - Verify data integrity
  - Document restoration procedure

Recovery Time Objective (RTO): 4 hours
Recovery Point Objective (RPO): 24 hours
```

---

## 7. Performance Requirements

### 7.1 Performance Targets

```yaml
Page Load Time (Mobile):
  Target: < 2 seconds
  Acceptable: < 3 seconds
  Critical: > 5 seconds (requires immediate action)

Page Load Time (Desktop):
  Target: < 1.5 seconds
  Acceptable: < 2.5 seconds
  Critical: > 4 seconds

Google PageSpeed Insights:
  Mobile Score: 85+ (target: 90+)
  Desktop Score: 95+ (target: 98+)

Core Web Vitals:
  Largest Contentful Paint (LCP): < 2.5s
  First Input Delay (FID): < 100ms
  Cumulative Layout Shift (CLS): < 0.1
  First Contentful Paint (FCP): < 1.8s
  Time to Interactive (TTI): < 3.8s

Calculator Performance:
  Calculation Time: < 100ms
  Chart Rendering: < 500ms
  Results Display: < 1 second total
```

### 7.2 Optimization Techniques

```yaml
Frontend Optimization:
  HTML:
    - Semantic markup
    - Minification (production)
    - Critical CSS inline
    - Defer non-critical CSS
  
  CSS:
    - Tailwind CSS (purged, tree-shaken)
    - Minification
    - Combine files where possible
    - Remove unused CSS (< 50KB total)
    - Use CSS Grid/Flexbox (avoid float)
  
  JavaScript:
    - ES6+ transpiled to ES5
    - Code splitting (load on demand)
    - Minification + uglification
    - Defer non-critical scripts
    - Async third-party scripts
    - Remove console logs (production)
    - Total bundle: < 100KB (gzipped)
  
  Images:
    - WebP format (JPEG/PNG fallback)
    - Lazy loading (native loading="lazy")
    - Responsive images (srcset, sizes)
    - Proper sizing (max 1920px width)
    - Compression (80% quality)
    - SVG for icons and logos
  
  Fonts:
    - System fonts preferred (fastest)
    - Google Fonts: font-display: swap
    - Preload critical fonts
    - Limit to 2 families, 4 weights max
    - Subset to Latin characters only

Backend Optimization:
  PHP:
    - OPcache enabled (zend_extension=opcache.so)
    - opcache.memory_consumption=256
    - opcache.max_accelerated_files=10000
    - opcache.revalidate_freq=2
    - php.ini tuning (memory_limit=256M)
  
  Database:
    - Query optimization (use indexes)
    - Limit post revisions (WP_POST_REVISIONS=5)
    - Clean up transients regularly
    - Object caching (Redis)
    - Database caching layer
    - Optimize tables weekly
  
  Caching Strategy:
    Browser Cache:
      - Static assets: 1 year (Cache-Control: max-age=31536000)
      - HTML pages: No cache (revalidate)
      - Images: 1 month
    
    CDN Cache (Cloudflare):
      - Calculator pages: 1 hour
      - Blog posts: 1 week
      - Homepage: 1 hour
      - Static assets: 1 week
    
    Page Cache (WP Rocket):
      - Lifespan: 24 hours
      - Preload cache: Yes
      - Mobile cache: Separate
      - User cache: No (unless logged in)
    
    Object Cache (Redis):
      - Database queries: 5-60 minutes (based on freshness needs)
      - Transients: As set by WordPress/plugins
      - Calculator results: 15 minutes (if server-side)
    
    Opcode Cache (OPcache):
      - PHP files: Until deployment
      - Revalidate: 2 seconds (development), Never (production)

Server Optimization:
  HTTP:
    - HTTP/2 enabled (multiplexing)
    - HTTP/3 (QUIC) if supported
    - Keep-Alive: ON (timeout 5s, max 100)
    - Persistent connections
  
  Compression:
    - Gzip: Level 6 (balance compression vs CPU)
    - Brotli: Level 6 (better than gzip)
    - Compress: HTML, CSS, JS, JSON, XML, SVG
    - Min size: 1KB
  
  Connection:
    - DNS prefetch for third-party domains
    - Preconnect to critical origins
    - Resource hints (preload, prefetch)
```

### 7.3 Monitoring & Alerts

```yaml
Performance Monitoring Tools:
  - Google PageSpeed Insights (weekly manual check)
  - GTmetrix (automated weekly reports)
  - Pingdom (uptime + response time monitoring)
  - Cloudflare Analytics (real-time)
  - WordPress Query Monitor (development only)

Alert Thresholds:
  Warning:
    - Page load time > 3 seconds
    - Server response time > 500ms
    - Error rate > 1%
    - CPU usage > 70%
    - Memory usage > 80%
  
  Critical:
    - Page load time > 5 seconds
    - Server response time > 1 second
    - Error rate > 5%
    - CPU usage > 90%
    - Memory usage > 95%
    - Downtime > 5 minutes

Notification Channels:
  - Email (all alerts)
  - SMS (critical alerts only)
  - Slack (development team channel)
```

---

## 8. API Specifications

### 8.1 REST API Endpoints

#### Base URL
```
https://financecalc.in/wp-json/fc/v1/
```

#### Authentication
```
No authentication required for public endpoints.
Admin endpoints require WordPress authentication (cookie or application password).
```

#### Endpoints

**1. Calculate SIP**
```yaml
POST /calculate/sip

Request Body:
{
  "monthlyInvestment": 5000,    # Number, required, 500-1000000
  "expectedReturn": 12,          # Number, required, 1-30 (annual %)
  "timePeriod": 10               # Number, required, 1-40 (years)
}

Response (200 OK):
{
  "success": true,
  "data": {
    "maturityAmount": 1161695,
    "totalInvested": 600000,
    "estimatedReturns": 561695,
    "wealthGain": 93.62
  }
}

Error Response (400 Bad Request):
{
  "success": false,
  "error": {
    "code": "invalid_parameter",
    "message": "Monthly investment must be between 500 and 1000000"
  }
}
```

**2. Calculate EMI**
```yaml
POST /calculate/emi

Request Body:
{
  "loanAmount": 5000000,         # Number, required, 50000-100000000
  "interestRate": 8.5,           # Number, required, 5-20 (annual %)
  "loanTenure": 20               # Number, required, 1-30 (years)
}

Response (200 OK):
{
  "success": true,
  "data": {
    "emi": 43391,
    "totalInterest": 5413840,
    "totalAmount": 10413840,
    "interestPercentage": 108.28,
    "amortizationSchedule": [ /* first 12 months */ ]
  }
}
```

**3. Get Interest Rates**
```yaml
GET /rates?bank=sbi&product=home_loan&tenure=240

Query Parameters:
  - bank: String, optional (e.g., "sbi", "hdfc", "icici")
  - product: String, optional (e.g., "home_loan", "fd", "rd")
  - tenure: Number, optional (months)

Response (200 OK):
{
  "success": true,
  "data": [
    {
      "bank": "SBI",
      "product": "Home Loan",
      "rateType": "Fixed",
      "tenure": 240,
      "interestRate": 8.5,
      "effectiveFrom": "2024-01-01",
      "lastUpdated": "2024-10-01T00:00:00Z"
    }
  ],
  "count": 1
}
```

**4. Log Calculator Usage**
```yaml
POST /log

Request Body:
{
  "type": "sip",
  "inputs": { /* calculator inputs */ },
  "results": { /* calculation results */ }
}

Response (201 Created):
{
  "success": true,
  "message": "Usage logged successfully"
}
```

### 8.2 Rate Limiting

```yaml
Public Endpoints:
  - 100 requests per minute per IP
  - 1000 requests per hour per IP
  - Exceeded: HTTP 429 (Too Many Requests)

Response Headers:
  X-RateLimit-Limit: 100
  X-RateLimit-Remaining: 95
  X-RateLimit-Reset: 1633024800
```

### 8.3 Error Codes

```yaml
400 Bad Request:
  - invalid_parameter
  - missing_required_field
  - validation_failed

404 Not Found:
  - endpoint_not_found
  - resource_not_found

429 Too Many Requests:
  - rate_limit_exceeded

500 Internal Server Error:
  - server_error
  - database_error
  - calculation_error

503 Service Unavailable:
  - maintenance_mode
  - temporary_unavailable
```

---

## 9. Deployment Architecture

### 9.1 Environments

```yaml
Development (Local):
  URL: http://financecalc.local
  Purpose: Feature development, testing
  Configuration:
    - WP_DEBUG: true
    - WP_DEBUG_LOG: true
    - WP_DEBUG_DISPLAY: true
    - SCRIPT_DEBUG: true
    - No caching
    - Sample data
  Tools:
    - Local by Flywheel OR Docker
    - Git (version control)
    - VS Code / PHPStorm

Staging:
  URL: https://staging.financecalc.in
  Purpose: Pre-production testing, QA
  Configuration:
    - WP_DEBUG: false
    - WP_DEBUG_LOG: true
    - Production-like environment
    - Same server specs as production
    - Password protected (HTTP auth)
    - robots.txt: Disallow all
  Access: Team only (HTTP auth: username/password)
  Database: Copy of production (sanitized)
  
Production:
  URL: https://financecalc.in
  Purpose: Live website
  Configuration:
    - WP_DEBUG: false
    - WP_DEBUG_LOG: true (file only, not displayed)
    - Full caching enabled
    - CDN enabled
    - Monitoring active
    - Backups automated
  Access: Public
```

### 9.2 Deployment Process

```yaml
Deployment Workflow:
  1. Development:
     - Create feature branch from 'develop'
     - Develop and test locally
     - Commit changes with meaningful messages
     - Push to GitHub
  
  2. Code Review:
     - Create Pull Request to 'develop'
     - Automated tests run (CI)
     - Team review and approval
     - Merge to 'develop'
  
  3. Staging Deployment:
     - Auto-deploy 'develop' to staging
     - QA testing on staging
     - Stakeholder review
     - Bug fixes (if needed)
  
  4. Production Deployment:
     - Create PR from 'develop' to 'main'
     - Final approval
     - Merge to 'main'
     - Manual trigger production deployment
     - Smoke tests
     - Monitor for 1 hour

Deployment Checklist:
  Pre-Deployment:
    ☐ All tests passing
    ☐ Code reviewed and approved
    ☐ Database migrations tested
    ☐ Staging validated
    ☐ Backup created (DB + files)
    ☐ Rollback plan documented
    ☐ Team notified
    ☐ Maintenance window scheduled (if needed)
  
  During Deployment:
    ☐ Enable maintenance mode (if major)
    ☐ Pull latest code
    ☐ Run database migrations
    ☐ Clear all caches (object, page, CDN)
    ☐ Update dependencies (composer, npm)
    ☐ Verify file permissions
    ☐ Test critical functionality
  
  Post-Deployment:
    ☐ Disable maintenance mode
    ☐ Monitor error logs (30 min)
    ☐ Check Google Analytics real-time
    ☐ Test all calculators
    ☐ Verify AdSense ads display
    ☐ Check mobile responsiveness
    ☐ Update documentation
    ☐ Notify team of success

Rollback Procedure:
  If issues detected within 1 hour:
    1. Immediately revert to previous version (git revert)
    2. Restore database from backup (if schema changed)
    3. Clear all caches
    4. Verify site stability
    5. Investigate issue
    6. Fix and re-deploy
```

### 9.3 CI/CD Pipeline (Phase 2)

```yaml
Tool: GitHub Actions

Workflow File (.github/workflows/deploy.yml):
  
  name: Deploy to Staging/Production
  
  on:
    push:
      branches: [develop, main]
  
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - Checkout code
        - Setup PHP 8.2
        - Install dependencies (composer)
        - Run PHP_CodeSniffer (WordPress standards)
        - Run PHPUnit tests
        - Run JavaScript tests (Jest)
    
    deploy-staging:
      needs: test
      if: github.ref == 'refs/heads/develop'
      runs-on: ubuntu-latest
      steps:
        - Checkout code
        - Deploy to staging server (SSH)
        - Clear caches
        - Run smoke tests
        - Notify team (Slack)
    
    deploy-production:
      needs: test
      if: github.ref == 'refs/heads/main'
      runs-on: ubuntu-latest
      environment: production
      steps:
        - Manual approval required
        - Checkout code
        - Create backup
        - Deploy to production server (SSH)
        - Run database migrations
        - Clear caches
        - Run smoke tests
        - Notify team (Slack + Email)

Automated Tests:
  - PHP Code Standards (PHPCS)
  - Unit Tests (PHPUnit)
  - JavaScript Tests (Jest)
  - Integration Tests (Postman/Newman)
  - E2E Tests (Playwright) [Phase 3]
```

---

## 10. Monitoring & Observability

### 10.1 Application Monitoring

```yaml
Tools:
  - Google Analytics 4 (user behavior)
  - Google Search Console (SEO health)
  - Cloudflare Analytics (performance, security)
  - Uptime Robot (uptime monitoring)
  - WordPress Query Monitor (performance debugging, dev only)
  - New Relic / AppDynamics (APM, optional Phase 3)

Metrics to Track:
  Traffic:
    - Pageviews (daily, weekly, monthly)
    - Unique visitors
    - Traffic sources (organic, direct, referral, social)
    - Top pages
    - Bounce rate
    - Session duration
    - Pages per session
  
  Performance:
    - Page load time (avg, median, p95, p99)
    - Server response time (TTFB)
    - Database query time
    - Cache hit ratio
    - Core Web Vitals (LCP, FID, CLS)
    - Error rate (4xx, 5xx)
  
  Business:
    - Calculator usage (by type)
    - Calculator completion rate
    - AdSense revenue (daily, monthly)
    - Ad impressions & clicks
    - CTR (click-through rate)
    - RPM (revenue per mille)
    - Affiliate clicks
  
  Security:
    - Failed login attempts
    - Blocked IPs
    - Malware scans (daily)
    - SSL certificate expiry
    - Plugin vulnerabilities

Dashboards:
  - Real-time: Cloudflare Analytics
  - Daily: Google Analytics 4 (custom dashboard)
  - Weekly: Performance report (automated email)
  - Monthly: Business metrics report (management)
```

### 10.2 Error Tracking

```yaml
Error Logging:
  PHP Errors:
    - Location: /wp-content/debug.log
    - Rotation: Daily, 7 days retention
    - Alert: Email on fatal errors
  
  JavaScript Errors:
    - Tool: Sentry (optional, Phase 2)
    - Capture: Uncaught exceptions, promise rejections
    - Context: User agent, page URL, user ID (if logged in)
  
  Server Errors:
    - Location: /var/log/nginx/error.log OR /var/log/apache2/error.log
    - Rotation: Weekly, 4 weeks retention
    - Alert: Email on 5xx errors

Error Response Workflow:
  1. Error detected and logged
  2. Alert sent (if severity > warning)
  3. Developer investigates
  4. Fix implemented and tested
  5. Deploy to production
  6. Verify fix in monitoring
  7. Post-mortem (if customer-facing)
```

### 10.3 Health Checks

```yaml
Automated Health Checks (every 5 minutes):
  1. Uptime:
     - HTTP status 200 on homepage
     - Response time < 2 seconds
  
  2. Functionality:
     - SIP calculator loads
     - EMI calculator loads
     - Database connection active
  
  3. External Services:
     - Google Analytics loading
     - AdSense ads displaying
     - CDN working
  
  4. Security:
     - SSL certificate valid
     - No malware detected
     - Firewall active

Alert Conditions:
  - 3 consecutive failures → Warning (email)
  - 5 consecutive failures → Critical (email + SMS)
  - Downtime > 5 minutes → Critical (escalate to on-call)

Incident Response:
  1. Acknowledge incident
  2. Assess severity
  3. Notify stakeholders (if public-facing)
  4. Investigate root cause
  5. Implement fix
  6. Verify resolution
  7. Document incident
  8. Schedule post-mortem
```

---

## 11. Development Workflow

### 11.1 Git Workflow

```yaml
Branch Strategy: Git Flow

Main Branches:
  main:
    - Production-ready code
    - Protected (no direct commits)
    - Merge via PR only
    - Tagged releases (v1.0.0, v1.1.0, etc.)
  
  develop:
    - Integration branch
    - Latest development changes
    - Auto-deploys to staging
    - Merge via PR from feature branches

Supporting Branches:
  feature/*:
    - New features
    - Branch from: develop
    - Merge back to: develop
    - Naming: feature/sip-calculator, feature/emi-calculator
  
  bugfix/*:
    - Non-critical bug fixes
    - Branch from: develop
    - Merge back to: develop
    - Naming: bugfix/calculator-validation-error
  
  hotfix/*:
    - Critical production fixes
    - Branch from: main
    - Merge back to: main AND develop
    - Naming: hotfix/security-patch
  
  release/*:
    - Release preparation
    - Branch from: develop
    - Merge back to: main AND develop
    - Naming: release/v1.1.0

Commit Message Format:
  type(scope): subject
  
  Types:
    - feat: New feature
    - fix: Bug fix
    - docs: Documentation
    - style: Formatting, no code change
    - refactor: Code refactoring
    - test: Adding tests
    - chore: Maintenance
  
  Examples:
    - feat(calculator): add SIP calculator with charts
    - fix(emi): correct amortization schedule calculation
    - docs(readme): update deployment instructions
```

### 11.2 Code Standards

```yaml
PHP:
  Standard: WordPress Coding Standards
  Tool: PHP_CodeSniffer (phpcs)
  Configuration: phpcs.xml.dist
  Rules:
    - PSR-12 compatible
    - WordPress-specific hooks and filters
    - Security: Sanitization, escaping, nonces
    - Documentation: PHPDoc blocks for all functions/methods
  
  Example:
    <?php
    /**
     * Calculate SIP maturity amount.
     *
     * @param float $principal Monthly investment amount.
     * @param float $rate Annual interest rate (percentage).
     * @param int $years Investment period in years.
     * @return float Maturity amount.
     */
    function fc_calculate_sip( $principal, $rate, $years ) {
        $r = ( $rate / 12 ) / 100;
        $n = $years * 12;
        return $principal * ( ( ( pow( 1 + $r, $n ) - 1 ) / $r ) * ( 1 + $r ) );
    }

JavaScript:
  Standard: Airbnb JavaScript Style Guide
  Tool: ESLint
  Configuration: .eslintrc.json
  Rules:
    - ES6+ features
    - Semicolons required
    - Single quotes for strings
    - 2-space indentation
    - No console logs in production
  
  Example:
    /**
     * SIP Calculator class
     */
    class SIPCalculator {
      constructor(containerId) {
        this.container = document.getElementById(containerId);
        this.init();
      }
      
      calculate(principal, rate, years) {
        const r = (rate / 12) / 100;
        const n = years * 12;
        return principal * (((Math.pow(1 + r, n) - 1) / r) * (1 + r));
      }
    }

CSS:
  Standard: BEM methodology + Tailwind utility classes
  Tool: Stylelint
  Configuration: .stylelintrc.json
  Rules:
    - Mobile-first approach
    - Use Tailwind utilities where possible
    - Custom CSS only when necessary
    - BEM naming for custom components
```

### 11.3 Documentation Standards

```yaml
Code Documentation:
  - All functions have PHPDoc/JSDoc comments
  - Complex logic explained with inline comments
  - README.md in each plugin/theme directory
  - API documentation for REST endpoints

Project Documentation:
  - README.md (project overview)
  - TECHNICAL_SPECIFICATION.md (this document)
  - CALCULATOR_FORMULAS.md (formula documentation)
  - SEO_KEYWORD_RESEARCH.md (SEO strategy)
  - CHANGELOG.md (version history)
  - DEPLOYMENT.md (deployment guide)

Inline Documentation:
  Good:
    // Calculate monthly rate from annual percentage
    const monthlyRate = (annualRate / 12) / 100;
  
  Bad:
    // Divide by 12 and 100
    const r = (rate / 12) / 100;
```

---

## 12. Testing Strategy

### 12.1 Testing Levels

```yaml
Unit Testing:
  Tool: PHPUnit (PHP), Jest (JavaScript)
  Coverage Target: 70%+
  Scope:
    - Calculator logic functions
    - Input validation functions
    - Utility functions
    - API endpoint logic
  
  Example (PHPUnit):
    class SIPCalculatorTest extends WP_UnitTestCase {
      public function test_sip_calculation() {
        $result = fc_calculate_sip(5000, 12, 10);
        $this->assertEquals(1161695, round($result));
      }
      
      public function test_invalid_input() {
        $this->expectException(InvalidArgumentException::class);
        fc_calculate_sip(-1000, 12, 10);
      }
    }

Integration Testing:
  Tool: Postman / Newman
  Scope:
    - REST API endpoints
    - Database operations
    - Third-party integrations (GA, AdSense)
  
  Test Cases:
    - POST /fc/v1/calculate/sip (valid input)
    - POST /fc/v1/calculate/sip (invalid input)
    - GET /fc/v1/rates (with filters)
    - POST /fc/v1/log (usage logging)

End-to-End Testing:
  Tool: Playwright OR Cypress
  Scope:
    - User workflows
    - Cross-browser testing
    - Mobile testing
  
  Test Scenarios:
    1. User navigates to SIP calculator
    2. Enters monthly investment: 10000
    3. Enters expected return: 12%
    4. Enters tenure: 5 years
    5. Clicks "Calculate"
    6. Verifies results display
    7. Clicks "Share" button
    8. Verifies share dialog opens

Performance Testing:
  Tool: Apache JMeter / k6
  Scope:
    - Load testing
    - Stress testing
    - Spike testing
  
  Scenarios:
    Normal Load:
      - 100 concurrent users
      - 5 requests/second
      - Duration: 10 minutes
      - Expected: < 2s response
    
    Peak Load:
      - 500 concurrent users
      - 25 requests/second
      - Duration: 5 minutes
      - Expected: < 3s response

Security Testing:
  Tool: WPScan, OWASP ZAP
  Scope:
    - Vulnerability scanning
    - Penetration testing
    - Authentication testing
  
  Tests:
    - SQL injection attempts
    - XSS (Cross-Site Scripting)
    - CSRF (Cross-Site Request Forgery)
    - File upload validation
    - Authentication bypass
```

### 12.2 Test Automation

```yaml
Continuous Integration:
  - All tests run on every commit to feature branches
  - Merge blocked if tests fail
  - Coverage report generated
  - Code quality checks (PHPCS, ESLint)

Manual Testing Checklist:
  Before Each Release:
    Functionality:
      ☐ All calculators produce correct results
      ☐ All forms submit successfully
      ☐ All links work (no 404s)
      ☐ Search works
    
    Design/UX:
      ☐ Layout correct on mobile (portrait & landscape)
      ☐ Layout correct on tablet
      ☐ Layout correct on desktop
      ☐ Images load correctly
      ☐ Fonts display correctly
    
    Cross-Browser (latest versions):
      ☐ Chrome
      ☐ Firefox
      ☐ Safari
      ☐ Edge
      ☐ Chrome Mobile
      ☐ Safari Mobile
    
    SEO:
      ☐ Title tags present
      ☐ Meta descriptions present
      ☐ Schema markup validates
      ☐ XML sitemap correct
      ☐ Robots.txt correct
    
    Performance:
      ☐ PageSpeed Insights: 85+ mobile
      ☐ PageSpeed Insights: 95+ desktop
      ☐ Images lazy load
      ☐ No console errors
    
    Monetization:
      ☐ AdSense ads display
      ☐ Ads placement correct
      ☐ Affiliate links work
```

---

## Appendix A: Technology Decision Matrix

```yaml
Decision: WordPress vs Custom Development
Winner: WordPress
Reasoning:
  Advantages:
    ✓ Faster time to market (weeks vs months)
    ✓ Lower development cost (30-50% savings)
    ✓ Rich plugin ecosystem
    ✓ Built-in content management
    ✓ Excellent SEO out-of-the-box
    ✓ Large talent pool
    ✓ Managed hosting options
  
  Disadvantages:
    ✗ Some performance overhead (mitigated with caching)
    ✗ Plugin dependency (mitigated with careful selection)
    ✗ Regular updates required (automated where possible)
  
  Conclusion: Benefits far outweigh drawbacks for this use case

Decision: Managed WordPress vs Self-Managed VPS
Winner: Managed WordPress (Cloudways/WP Engine)
Reasoning:
  Phase 1-2 (0-150K pageviews):
    ✓ Zero DevOps overhead
    ✓ Optimized for WordPress
    ✓ Built-in staging, backups, CDN
    ✓ Focus on content, not infrastructure
    ✓ Auto-scaling (Cloudways)
  
  Phase 3+ (150K+ pageviews):
    Consider: Self-managed VPS for cost optimization
    ✓ Better cost-to-performance ratio
    ✓ More control
    ✗ Requires DevOps knowledge

Decision: React/Vue vs Vanilla JavaScript
Winner: Vanilla JavaScript (Phase 1-2), React (Phase 3+)
Reasoning:
  Phase 1-2:
    ✓ Faster development (no build setup needed)
    ✓ Smaller bundle size (better performance)
    ✓ Sufficient for basic calculators
    ✓ Lower learning curve for team
  
  Phase 3 (Advanced features):
    ✓ React: Better for complex UIs
    ✓ Component reusability
    ✓ State management (Redux)
    ✓ User accounts, saved calculations
```

---

## Appendix B: Glossary

```yaml
AdSense: Google's advertising platform for publishers
CDN: Content Delivery Network
CLS: Cumulative Layout Shift (Core Web Vital)
CPC: Cost Per Click
CSS: Cascading Style Sheets
FCP: First Contentful Paint
FID: First Input Delay (Core Web Vital)
LCP: Largest Contentful Paint (Core Web Vital)
REST API: Representational State Transfer Application Programming Interface
RPM: Revenue Per Mille (per thousand impressions)
SIP: Systematic Investment Plan
TTI: Time to Interactive
TTFB: Time to First Byte
VPS: Virtual Private Server
WAF: Web Application Firewall
```

---

## Document Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Technical Lead | [Name] | | |
| Project Manager | [Name] | | |
| CTO | [Name] | | |

---

**End of Technical Specification Document**

**Next Steps:**
1. Review document with team
2. Finalize hosting provider
3. Setup development environment
4. Begin Phase 1 implementation

**Document will be updated as project progresses.**
