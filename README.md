# Matrix Homeserver (Synapse + Element + Traefik)

A production-grade, containerized Matrix stack with automated SSL (Let's Encrypt), PostgreSQL performance tuning, and the Element web client.

## 🚀 Quick Start

### 1. Prerequisites

- **Domain**: You must own a domain (e.g., `example.com`).
- **DNS**: Create A/AAAA records for:
  - `matrix.yourdomain.com`
  - `chat.yourdomain.com`
- **Network**: Ensure your router/firewall forwards:
  - Public `80` -> Host `8080`
  - Public `443` -> Host `8443`

### 2. Configuration

Run the following command to replace the placeholder domain with your actual domain:

```bash
# Replace 'yourdomain.com' with your real domain
grep -rli 'YOUR_DOMAIN' * | xargs -i@ sed -i 's/YOUR_DOMAIN/yourdomain.com/g' @
```

### 3. Initialize & Deploy

```bash
# 1. Set permissions and generate signing keys
./setup.sh

# 2. Update passwords (CRITICAL)
# Edit docker-compose.yml and config/synapse/homeserver.yaml
# to change 'synapse_password_change_me' to a strong password.

# 3. Launch the stack
docker-compose up -d
```

## 🏗️ Architecture

- **Traefik v3**: Entry point for all traffic. Handles SSL termination and routing.
- **Synapse**: The core Matrix Homeserver.
- **PostgreSQL 16**: High-performance database.
- **Element Web**: The browser-based client.

## 🛠️ Maintenance & Operations

### Viewing Logs

```bash
docker-compose logs -f synapse
docker-compose logs -f traefik
```

### Managing Users

To create an admin user via the command line:

```bash
docker exec -it synapse register_new_matrix_user -c /data/homeserver.yaml http://localhost:8008
```

### SSL Certificates

Certificates are managed automatically by Traefik and stored in `./letsencrypt/acme.json`. The `setup.sh` script ensures this file has the correct `600` permissions.

## ⚙️ Performance Tuning

This stack is optimized for a host with **8GB RAM**:

- **Postgres**: Uses custom `shared_buffers` and `effective_cache_size`.
- **Synapse**: Configured with a `global_factor` of `2.0` for enhanced caching.

## 📂 Directory Structure

- `config/synapse`: Configuration and data for the Synapse homeserver.
- `config/element`: Configuration for the Element web client.
- `letsencrypt`: Let's Encrypt SSL storage.
- `setup.sh`: Initialization script.
