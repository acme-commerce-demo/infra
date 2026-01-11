# ACME Commerce Infrastructure

Infrastructure configuration for local development of the ACME Commerce platform.

## Services

| Service | Port | Description |
|---------|------|-------------|
| api-gateway | 8080 | API Gateway (Go) |
| inventory-service | 8081 | Inventory management (Go) |
| user-service | 3001 | User authentication (Node.js) |
| order-service | 3002 | Order management (Node.js) |
| payment-service | 3003 | Payment processing (Node.js) |
| notification-service | 3004 | Notifications (Node.js) |

## Infrastructure

| Service | Port | Description |
|---------|------|-------------|
| PostgreSQL | 5432 | Primary database |
| Redis | 6379 | Caching and sessions |
| RabbitMQ | 5672, 15672 | Message queue (management UI on 15672) |

## Quick Start

1. Clone all service repositories into a parent directory:
   ```bash
   mkdir acme-commerce && cd acme-commerce
   gh repo clone acme-commerce-demo/api-gateway
   gh repo clone acme-commerce-demo/inventory-service
   gh repo clone acme-commerce-demo/user-service
   gh repo clone acme-commerce-demo/order-service
   gh repo clone acme-commerce-demo/payment-service
   gh repo clone acme-commerce-demo/notification-service
   gh repo clone acme-commerce-demo/infra
   ```

2. Copy infra files to parent directory:
   ```bash
   cp infra/docker-compose.yml infra/docker-compose.dev.yml .
   cp infra/.env.example .env
   mkdir -p scripts && cp infra/scripts/init-db.sql scripts/
   ```

3. Configure environment:
   ```bash
   # Edit .env with your JFrog credentials and API keys
   vim .env
   ```

4. Start all services:
   ```bash
   # Production-like containers
   docker-compose up -d

   # Development with hot reload
   docker-compose -f docker-compose.yml -f docker-compose.dev.yml up
   ```

## Environment Variables

Copy `.env.example` to `.env` and configure:

| Variable | Description |
|----------|-------------|
| `JF_URL` | JFrog Artifactory URL |
| `JF_ACCESS_TOKEN` | JFrog access token for pulling @acme packages |
| `STRIPE_SECRET_KEY` | Stripe API key (payment-service) |
| `STRIPE_WEBHOOK_SECRET` | Stripe webhook secret |
| `SENDGRID_API_KEY` | SendGrid API key (notification-service) |
| `TWILIO_ACCOUNT_SID` | Twilio account SID |
| `TWILIO_AUTH_TOKEN` | Twilio auth token |

## Database

The `scripts/init-db.sql` script automatically creates databases and schemas for each service:

- `users_db` - User accounts and sessions
- `orders_db` - Orders and order items
- `payments_db` - Payments and refunds
- `inventory_db` - Products and reservations

## Useful Commands

```bash
# View logs
docker-compose logs -f [service-name]

# Restart a service
docker-compose restart [service-name]

# Rebuild a service
docker-compose up -d --build [service-name]

# Stop everything
docker-compose down

# Stop and remove volumes (clean slate)
docker-compose down -v

# Access PostgreSQL
docker exec -it acme-postgres psql -U acme

# Access Redis
docker exec -it acme-redis redis-cli

# RabbitMQ Management UI
open http://localhost:15672  # guest:guest or acme:acme_dev_password
```
