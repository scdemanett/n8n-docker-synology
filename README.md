# n8n Docker Setup for Synology NAS

A Docker Compose configuration for running [n8n](https://n8n.io) workflow automation tool on Synology NAS with PostgreSQL database.

## 🚀 Features

- n8n setup with PostgreSQL database
- **Optimized for Synology NAS** with proper permissions and paths
- **Health checks** for both database and n8n services
- **Resource limits** to prevent system overload
- **Persistent storage** for workflows and data

## 📋 Prerequisites

- Synology NAS with Docker/Container Manager installed
- Domain name with HTTPS configured (e.g., via Synology DDNS)
- Basic knowledge of Docker and command line
- Sufficient storage space on Volume 1

## 🛠️ Quick Start

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd n8n-docker-synology
```

### 2. Configure Environment

Before starting, you **MUST** update the following values in `docker-compose.yml`:

#### Required Changes:

1. **Domain Configuration**
   ```yaml
   N8N_HOST: n8n.yourname.synology.me  # Replace with your domain
   WEBHOOK_URL: https://n8n.yourname.synology.me  # Replace with your domain
   ```

2. **Database Password**
   ```yaml
   # In db service:
   POSTGRES_PASSWORD: n8npass  # Change to a strong password
   
   # In n8n service:
   DB_POSTGRESDB_PASSWORD: n8npass  # Must match the password above
   ```

3. **Encryption Key**
   Generate a new encryption key:
   ```bash
   openssl rand -hex 32
   ```
   Then replace:
   ```yaml
   N8N_ENCRYPTION_KEY: <your-generated-key>
   ```

4. **Timezone** (optional)
   ```yaml
   GENERIC_TIMEZONE: America/New_York  # Change to your timezone
   TZ: America/New_York  # Change to your timezone
   ```

### 3. Create Required Directories

SSH into your Synology NAS and create the necessary directories:

```bash
sudo mkdir -p /volume1/docker/n8n/db
sudo mkdir -p /volume1/docker/n8n/data
sudo mkdir -p /volume1/docker/n8n/files
```

### 4. Deploy

```bash
docker-compose up -d
```

## 🔧 Configuration Details

### Directory Structure

```
/volume1/docker/n8n/
├── db/          # PostgreSQL database files
├── data/        # n8n configuration and workflows
└── files/       # File storage for n8n
```

### Port Configuration

- **n8n Web UI**: Port 5678
- Access via: `https://your-domain:5678` or through reverse proxy

### Environment Variables

| Variable             | Description                    | Default  |
|----------------------|--------------------------------|----------|
| `N8N_HOST`           | Your domain name               | Required |
| `WEBHOOK_URL`        | Full URL for webhooks          | Required |
| `N8N_ENCRYPTION_KEY` | Encryption key for credentials | Required |
| `POSTGRES_PASSWORD`  | Database password              | Required |
| `N8N_PORT`           | n8n listening port             | 5678     |
| `N8N_PROTOCOL`       | Protocol (http/https)          | https    |
| `N8N_SECURE_COOKIE`  | Enable secure cookies          | true     |

## 🔒 Security Considerations

1. **Always generate a new encryption key** - Never use the default one
2. **Use strong passwords** for the PostgreSQL database
3. **Enable HTTPS** with a valid SSL certificate
4. **Regular backups** of your data directory
5. **Keep containers updated** with the latest security patches

## 🚦 Health Checks

Both services include health checks:
- **PostgreSQL**: Checks database availability every 10 seconds
- **n8n**: Verifies the service is responding on port 5678

## 📊 Resource Management

Default resource limits:
- **PostgreSQL**: 512MB memory limit, 256MB reservation
- **n8n**: 2GB memory limit, 512MB reservation

Adjust these based on your workload and available resources.

## 🔧 Maintenance

### Backup

Regular backups are crucial. Back up these directories:
```bash
/volume1/docker/n8n/data/  # Workflows and credentials
/volume1/docker/n8n/db/    # Database
```

### Updates

To update n8n to the latest version:
```bash
docker-compose pull
docker-compose up -d
```

### Logs

View logs:
```bash
# All services
docker-compose logs -f

# Only n8n
docker-compose logs -f n8n

# Only database
docker-compose logs -f db
```

## 🚨 Troubleshooting

### Permission Issues

If you encounter permission errors, ensure the n8n service runs as root (user: 0:0) which is configured by default.

### Database Connection Failed

1. Check if the database container is healthy:
   ```bash
   docker-compose ps
   ```
2. Verify passwords match in both services
3. Check database logs:
   ```bash
   docker-compose logs db
   ```

### Cannot Access Web UI

1. Verify the container is running:
   ```bash
   docker ps | grep n8n
   ```
2. Check firewall rules for port 5678
3. Verify your domain/DNS configuration

### Webhook Issues

Ensure your `WEBHOOK_URL` is publicly accessible and matches your reverse proxy configuration.

## 📚 Additional Resources

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [Docker Documentation](https://docs.docker.com/)
- [Synology Docker Guide](https://kb.synology.com/en-us/DSM/help/Docker/docker_desc)

## 📝 License

This configuration is provided as-is. Please refer to n8n's license for the application itself.

## 🤝 Contributing

Feel free to submit issues and enhancement requests!

---

**⚠️ Important**: Remember to never commit sensitive information like passwords or encryption keys to version control. 