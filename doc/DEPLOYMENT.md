---
title: Haland Petcare Deployment Guide
version: 1.0.0
last_modified: 2026-06-29
owner: Lead Architect
status: Draft
depends_on: [PROJECT_SPECIFICATION.md, TESTING_STRATEGY.md, SECURITY.md]
referenced_by: [PROJECT_SPECIFICATION.md, TESTING_STRATEGY.md, DEVELOPMENT_STANDARDS.md]
---

# Haland Petcare Deployment Guide

## Related Documents
- [PROJECT_SPECIFICATION.md](PROJECT_SPECIFICATION.md) — Master project specification
- [TESTING_STRATEGY.md](TESTING_STRATEGY.md) — Testing strategy and UAT criteria
- [SECURITY.md](SECURITY.md) — Security specification
- [DATABASE.md](DATABASE.md) — Database architecture and rules
- [GLOSSARY.md](GLOSSARY.md) — Domain terminology

---

## Overview

This document defines the deployment architecture, CI/CD pipeline, environment configuration, backup strategy, and rollback procedures for Haland Petcare.

The primary deployment target is Railway.

---

## Deployment Architecture

```
┌─────────────────┐
│   Cloudflare    │
│   DNS + Proxy   │
└────────┬────────┘
         │
┌────────▼────────┐
│   Railway App   │
│   Laravel 12    │
└────────┬────────┘
         │
┌────────▼────────┐
│   Railway MySQL │
│   Primary DB    │
└─────────────────┘
```

### Components
- **Application Server:** Railway Laravel service
- **Database:** Railway MySQL 8
- **Queue Worker:** Railway worker service running `php artisan queue:work`
- **Scheduler:** Railway cron service running `php artisan schedule:run`
- **Object Storage:** Railway volumes or S3-compatible storage for attachments
- **CDN:** Cloudflare for static assets and DDoS protection

---

## Environments

| Environment | Purpose | Branch |
|---|---|---|
| Development | Local development and feature testing | feature/* |
| Staging | Pre-production validation | develop |
| Production | Live clinic operations | main |

---

## Environment Variables

### Application
| Variable | Description | Example |
|---|---|---|
| APP_NAME | Application name | Haland Petcare |
| APP_ENV | Environment | production |
| APP_KEY | Laravel encryption key | base64:... |
| APP_DEBUG | Debug mode | false |
| APP_URL | Public URL | https://app.halandpetcare.com |
| APP_TIMEZONE | Application timezone | UTC |

### Database
| Variable | Description | Example |
|---|---|---|
| DB_CONNECTION | Database driver | mysql |
| DB_HOST | Database host | railway |
| DB_PORT | Database port | 3306 |
| DB_DATABASE | Database name | haland_petcare |
| DB_USERNAME | Database user | laravel |
| DB_PASSWORD | Database password | [secret] |

### Queue and Cache
| Variable | Description | Example |
|---|---|---|
| QUEUE_CONNECTION | Queue driver | database |
| CACHE_DRIVER | Cache driver | file or redis |
| SESSION_DRIVER | Session driver | database |
| SESSION_LIFETIME | Session timeout in minutes | 30 |

### Mail / Notifications (Future)
| Variable | Description | Example |
|---|---|---|
| MAIL_MAILER | Mail driver | smtp |
| MAIL_HOST | SMTP host | smtp.mailgun.org |
| MAIL_FROM_ADDRESS | Sender email | noreply@halandpetcare.com |

### Security
| Variable | Description | Example |
|---|---|---|
| FORCE_HTTPS | Force HTTPS in production | true |
| TRUSTED_PROXIES | Trusted proxy IPs | * |

---

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
name: CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      mysql:
        image: mysql:8
        env:
          MYSQL_ROOT_PASSWORD: root
          MYSQL_DATABASE: haland_test
    steps:
      - uses: actions/checkout@v4
      - uses: shivammathur/setup-php@v2
        with:
          php-version: '8.3'
      - run: composer install
      - run: php artisan key:generate --env=testing
      - run: php artisan migrate --env=testing
      - run: php artisan test

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Railway Staging
        run: railway up --environment staging
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Railway Production
        run: railway up --environment production
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
```

---

## Database Migration Strategy

1. Migrations are run automatically during deployment.
2. Always test migrations against a copy of production data in staging.
3. Use transactions for complex migrations.
4. Never delete or rename columns in a way that breaks existing data without a transition plan.
5. Keep migrations backward-compatible where possible.

---

## Backup Strategy

### Automated Backups
- Daily full database backups at 02:00 UTC.
- Retain 30 daily backups.
- Retain 12 monthly backups.
- Store backups in geographically separate object storage.

### Manual Backups
- Create backup before every production deployment.
- Create backup before running risky migrations.

### Backup Verification
- Restore a backup to staging monthly to verify integrity.

---

## Rollback Procedures

### Application Rollback
1. Identify the last known good deployment.
2. Use Railway dashboard or CLI to redeploy previous version.
3. Verify health checks pass.
4. Monitor error logs.

### Database Rollback
1. Stop application traffic.
2. Restore from backup taken before the failed change.
3. Run any necessary data reconciliation.
4. Restart application.
5. Verify data integrity.

### Rollback Triggers
- Deployment causes critical errors.
- Data corruption detected.
- Performance degradation exceeds thresholds.

---

## Monitoring and Alerting

### Logs
- Application logs aggregated in Railway logs.
- Error logs sent to error tracking service (e.g., Sentry).

### Metrics
- Request latency
- Error rate
- Queue length
- Database connection count
- Disk usage

### Alerts
- Alert on:
  - Error rate > 1%
  - Queue backlog > 100 jobs
  - Database storage > 80%
  - Failed backups

---

## SSL/TLS

- TLS 1.2+ enforced.
- HSTS header enabled.
- Automatic certificate management via Cloudflare / Railway.

---

## Domain Configuration

- Production: `https://app.halandpetcare.com`
- Staging: `https://staging.halandpetcare.com`
- DNS managed via Cloudflare.
- Enable proxying and DDoS protection.

---

## Performance Optimization

- Enable OPcache in production.
- Cache configuration, routes, and views.
- Use Redis for cache and sessions when scaling.
- Optimize images before upload.
- Use eager loading for all relationships.
- Paginate large datasets.

---

## Post-Deployment Checklist

- [ ] Application health check passes.
- [ ] Database migrations successful.
- [ ] Queue worker running.
- [ ] Scheduler running.
- [ ] All environment variables set.
- [ ] SSL certificate valid.
- [ ] Smoke tests pass.
- [ ] Error tracking active.
- [ ] Backups scheduled.

---

## Disaster Recovery

### RPO (Recovery Point Objective)
- Maximum data loss: 24 hours.

### RTO (Recovery Time Objective)
- System restored within 4 hours.

### Recovery Steps
1. Assess incident scope.
2. Activate backup restoration.
3. Verify data integrity.
4. Redirect traffic to recovered environment.
5. Communicate with stakeholders.
6. Conduct post-incident review.

---

## Golden Rule

Production deployments must be repeatable, reversible, and verifiable. Never deploy untested code to production.