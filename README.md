# n8n Docker Setup for Synology NAS

A Docker Compose setup for running n8n with PostgreSQL on Synology NAS, optimized for security and ease of deployment via Portainer.

## 🚀 Features

- **Environment variable configuration** for security and portability
- **Synology NAS optimized** with proper volume mapping
- **Portainer compatible** with easy UI deployment
- **PostgreSQL 17** database with health checks
- **HTTPS ready** with proper SSL cookie configuration
- **Automatic restarts** and health monitoring

## 📋 Prerequisites

- Synology NAS with Docker installed
- SSH access to your Synology NAS
- Domain name configured (for HTTPS setup)
- OpenSSL for key generation

## ⚡ Quick Start

### Option 1: Docker Compose (Command Line)

1. **Clone this repository** or download the files:
   ```bash
   git clone <repository-url>
   cd n8n-docker-synology
   ```

2. **Create environment file**:
   ```bash
   cp env.example .env
   ```

3. **Generate encryption key**:
   ```bash
   openssl rand -hex 32
   ```

4. **Edit `.env` file** with your values:
   - Update `POSTGRES_PASSWORD` with a strong password
   - Set `N8N_ENCRYPTION_KEY` to the generated key
   - Configure `N8N_HOST` and `WEBHOOK_URL` with your domain
   - Adjust other settings as needed

5. **Create data directories** (SSH into Synology):
   ```bash
   mkdir -p /volume1/docker/n8n/db
   mkdir -p /volume1/docker/n8n/data
   mkdir -p /volume1/docker/n8n/files
   ```

6. **Deploy**:
   ```bash
   docker-compose up -d
   ```

### Option 2: Portainer (Recommended)

1. **Generate encryption key**:
   ```bash
   openssl rand -hex 32
   ```

2. **Create data directories** (SSH into Synology):
   ```bash
   mkdir -p /volume1/docker/n8n/db
   mkdir -p /volume1/docker/n8n/data
   mkdir -p /volume1/docker/n8n/files
   ```

3. **Deploy in Portainer**:
   - Go to **Stacks** → **Add stack**
   - Name your stack (e.g., `n8n`)
   - Copy and paste the `docker-compose.yml` contents
   - Add environment variables (see section below)
   - Click **Deploy the stack**

## 🔧 Environment Variables

All configuration is done via environment variables. See `env.example` for a complete template.

### Required Variables

| Variable             | Description        | Example                              |
|----------------------|--------------------|--------------------------------------|
| `POSTGRES_PASSWORD`  | Database password  | `your_secure_password_here`          |
| `N8N_ENCRYPTION_KEY` | n8n encryption key | Generate with `openssl rand -hex 32` |
| `N8N_HOST`           | Your domain name   | `n8n.yourname.synology.me`           |
| `WEBHOOK_URL`        | Full webhook URL   | `https://n8n.yourname.synology.me`   |

### Optional Variables (with defaults)

| Variable            | Default               | Description                         |
|---------------------|-----------------------|-------------------------------------|
| `POSTGRES_DB`       | `n8n`                 | Database name                       |
| `POSTGRES_USER`     | `n8ndbuser`           | Database username                   |
| `N8N_PROTOCOL`      | `https`               | Protocol (http/https)               |
| `N8N_PORT`          | `5678`                | External port                       |
| `N8N_SECURE_COOKIE` | `true`                | Secure cookies (set false for HTTP) |
| `TIMEZONE`          | `America/New_York`    | System timezone                     |
| `DATA_PATH`         | `/volume1/docker/n8n` | Data storage path                   |

### Portainer Environment Variables Setup

In Portainer's Stack interface, add these in the "Environment variables" section:

| Name                 | Value                              | Notes                          |
|----------------------|------------------------------------|--------------------------------|
| `POSTGRES_DB`        | `n8n`                              | Database name                  |
| `POSTGRES_USER`      | `n8ndbuser`                        | Database user                  |
| `POSTGRES_PASSWORD`  | `your_secure_password`             | **Must be strong!**            |
| `N8N_HOST`           | `n8n.yourname.synology.me`         | Your domain                    |
| `WEBHOOK_URL`        | `https://n8n.yourname.synology.me` | Full URL                       |
| `N8N_ENCRYPTION_KEY` | `generated_key_from_openssl`       | **Required!**                  |
| `N8N_PROTOCOL`       | `https`                            | Or `http` for testing          |
| `N8N_PORT`           | `5678`                             | External port                  |
| `N8N_SECURE_COOKIE`  | `true`                             | Set `false` if not using HTTPS |
| `TIMEZONE`           | `America/New_York`                 | Your timezone                  |
| `DATA_PATH`          | `/volume1/docker/n8n`              | Synology path                  |

## 📁 Directory Structure

```
/volume1/docker/n8n/
├── db/          # PostgreSQL data
├── data/        # n8n workflows and settings
└── files/       # n8n file storage
```

## 🛠 Deployment Steps

### Docker Compose Deployment

1. Ensure all prerequisites are met
2. Configure your `.env` file
3. Create required directories
4. Run: `docker-compose up -d`
5. Check logs: `docker-compose logs -f`

### Portainer Deployment

1. **Pre-deployment checklist**:
   - ✅ Generated encryption key
   - ✅ Created data directories
   - ✅ Configured domain/DNS
   - ✅ Port 5678 available

2. **In Portainer**:
   - Navigate to **Stacks** → **Add stack**
   - Name your stack (e.g., `n8n`)
   - Paste `docker-compose.yml` contents
   - Add all environment variables from table above
   - Click **Deploy the stack**

3. **Monitor deployment**:
   - Check container health status
   - View logs if any issues occur
   - Wait for both containers to be healthy

## 🎯 Post-Deployment

1. **Access n8n**:
   - HTTP: `http://your-synology-ip:5678`
   - HTTPS: `https://your-domain.synology.me`

2. **Complete setup wizard**:
   - Create admin account
   - Configure additional settings
   - Test webhook functionality

3. **Verify functionality**:
   - Database connection working
   - Workflows can be created
   - File storage accessible

## 🔒 Security Notes

- **Never commit `.env`** to version control (already in `.gitignore`)
- **Use strong passwords** (minimum 16 characters with mixed case, numbers, symbols)
- **Generate unique encryption key** for each installation
- **Keep environment variables secure**:
  ```bash
  chmod 600 .env  # If using Docker Compose
  ```
- **Use HTTPS in production** with valid SSL certificates
- **Regular backups** of `/volume1/docker/n8n/` directory
- **Keep containers updated** regularly

## 🚨 Troubleshooting

### Common Issues

| Issue                          | Solution                                          |
|--------------------------------|---------------------------------------------------|
| **Database connection failed** | Ensure `POSTGRES_PASSWORD` matches in all places  |
| **Cookie/session errors**      | Set `N8N_SECURE_COOKIE=false` if not using HTTPS  |
| **Permission denied**          | Check directory permissions and ownership         |
| **Port already in use**        | Change `N8N_PORT` to an available port            |
| **Webhook not working**        | Verify `WEBHOOK_URL` and firewall settings        |
| **Container won't start**      | Check logs: `docker-compose logs` or in Portainer |

### Health Checks

Both containers include health checks:
- **PostgreSQL**: Uses `pg_isready` command
- **n8n**: Checks port 5678 availability

Monitor these in Portainer's container view or via:
```bash
docker-compose ps
```

### Log Monitoring

**Docker Compose**:
```bash
docker-compose logs -f          # All services
docker-compose logs -f n8n      # n8n only
docker-compose logs -f db       # Database only
```

**Portainer**: Use the built-in log viewer in the container details.

## 🔄 Updates

### Updating n8n

1. **Backup your data** first
2. **Pull new image**:
   ```bash
   docker-compose pull
   docker-compose up -d
   ```
3. **In Portainer**: Redeploy the stack

### Updating PostgreSQL

⚠️ **Major version updates require special care** - backup database first!

## 📦 What's Included

- **docker-compose.yml**: Main orchestration file
- **env.example**: Environment variables template  
- **README.md**: This comprehensive guide
- **.gitignore**: Protects sensitive files

## 🤝 Portainer Benefits

- **Secure variable storage**: Environment variables encrypted
- **Easy updates**: Redeploy stacks without file editing
- **Visual monitoring**: Container health, logs, and metrics
- **User-friendly**: No command line required
- **Multi-environment**: Reuse configurations across environments

## 📞 Support

For issues specific to this setup, check the troubleshooting section above. For n8n-specific questions, refer to the [official n8n documentation](https://docs.n8n.io/).

## 📄 License

This configuration is provided as-is for educational and production use. 