# Universal Dev Stack

A ready-to-use Docker Compose stack for local development with databases, message brokers, search engines, and development tools.

## Quick Start

```bash
# Start all services
docker-compose up -d

# Start a specific service
docker-compose up -d mysql

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Stop and remove data volumes (⚠️ deletes all data)
docker-compose down -v
```

## Services

### 1. Relational Databases (SQL)

| Service | Port | Connection String | Credentials |
|---------|------|-------------------|-------------|
| **MySQL** | `3306` | `mysql://root:rootpassword@localhost:3306/dev_db` | root / rootpassword |
| **PostgreSQL** | `5432` | `postgresql://postgres:rootpassword@localhost:5432/dev_db` | postgres / rootpassword |

**Management UIs:**
- **phpMyAdmin** (MySQL): http://localhost:8081
- **pgAdmin** (PostgreSQL): http://localhost:8082 (admin@admin.com / adminpassword)

### 2. NoSQL & Caching

| Service | Port | Connection String | Notes |
|---------|------|-------------------|-------|
| **MongoDB** | `27017` | `mongodb://localhost:27017` | No auth by default |
| **Redis** | `6379` | `redis://localhost:6379` | |

### 3. Search & Analytics

| Service | Port | URL | Notes |
|---------|------|-----|-------|
| **Elasticsearch** | `9200` | http://localhost:9200 | Security disabled for dev |

### 4. Message Brokers

| Service | Ports | URL | Notes |
|---------|-------|-----|-------|
| **RabbitMQ** | `5672` (AMQP), `15672` (UI) | http://localhost:15672 | Management UI included |
| **Kafka** | `9092` | `localhost:9092` | KRaft mode (no Zookeeper) |

### 5. Proxy & Gateway

| Service | Ports | URL | Notes |
|---------|-------|-----|-------|
| **Traefik** | `80` (HTTP), `8080` (Dashboard) | http://localhost:8080 | Auto-discovers Docker services |

### 6. Email Testing

| Service | Ports | URL | Notes |
|---------|-------|-----|-------|
| **MailDev** | `1025` (SMTP), `8025` (UI) | http://localhost:8025 | Catch-all SMTP server |

## Configuration

All data is persisted in the `./data/` directory:

```
data/
├── mysql/
├── postgres/
├── mongodb/
├── redis/
├── elasticsearch/
├── rabbitmq/
└── kafka/
```

## Common Use Cases

### Connect your app to a database

```yaml
# In your app's docker-compose.yml
services:
  your-app:
    build: .
    environment:
      DATABASE_URL: mysql://root:rootpassword@host.docker.internal:3306/dev_db
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

### Test email sending

Point your app's SMTP to `localhost:1025` and view received emails at http://localhost:8025.

### Use Traefik as reverse proxy

Add these labels to your services:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myapp.rule=Host(`myapp.localhost`)"
  - "traefik.http.routers.myapp.entrypoints=web"
```

## Troubleshooting

**Port already in use?**
- Check if another service is running on that port
- Modify the port mapping in `docker-compose.yml` (e.g., `"3307:3306"`)

**Services won't start?**
```bash
# Check Docker is running
docker info

# Remove stale volumes and restart
docker-compose down -v
docker-compose up -d
```

**View service logs:**
```bash
docker-compose logs -f <service-name>
```

## Security Notice

⚠️ **For local development only.** This stack uses weak/default passwords and disabled security features for convenience. Never expose these services to the internet.
