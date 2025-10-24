# Racing Tips Notebook - Deployment Guide

## Quick Start (Recommended)

The easiest way to deploy Racing Tips Notebook is using the pre-built Docker image:

### Step 1: Create a directory

```bash
mkdir racing-tips-notebook
cd racing-tips-notebook
```

### Step 2: Create docker-compose.yml

```yaml
services:
  racing_tips_notebook:
    image: domocarroll/racing-tips-notebook:latest
    ports:
      - "8502:8502"  # Web UI
      - "5055:5055"  # API
    environment:
      # Required: Your AI API key (choose one)
      - ANTHROPIC_API_KEY=your_anthropic_key_here
      # OR
      # - OPENAI_API_KEY=your_openai_key_here
      
      # For remote access, set this to your server's IP or domain
      # - API_URL=http://192.168.1.100:5055
      
      # Database configuration
      - SURREAL_URL=ws://localhost:8000/rpc
      - SURREAL_USER=root
      - SURREAL_PASSWORD=root
      - SURREAL_NAMESPACE=racing_tips
      - SURREAL_DATABASE=production
    volumes:
      - ./notebook_data:/app/data
      - ./surreal_data:/mydata
    restart: always
```

### Step 3: Start the container

```bash
docker compose up -d
```

### Step 4: Access the application

- **Web Interface**: http://localhost:8502
- **API Documentation**: http://localhost:5055/docs

---

## Building from Source

If you want to customize the application or build your own Docker image:

### Prerequisites

- Docker and Docker Compose
- Git

### Step 1: Clone the repository

```bash
git clone https://github.com/domocarroll/racing-tips-notebook.git
cd racing-tips-notebook
```

### Step 2: Configure environment

```bash
cp docker.env.example docker.env
# Edit docker.env and add your API keys
nano docker.env
```

### Step 3: Build and run

```bash
docker compose -f docker-compose.single.yml up -d --build
```

This will:
1. Build the Docker image from source
2. Install all dependencies
3. Build the Next.js frontend
4. Start SurrealDB, API, and Frontend services
5. Make the application available at http://localhost:8502

---

## Remote Server Deployment

When deploying to a remote server (not localhost), you need to configure the `API_URL` so the frontend knows where to find the API.

### Example: Deploy to a server at 192.168.1.100

```yaml
services:
  racing_tips_notebook:
    image: domocarroll/racing-tips-notebook:latest
    ports:
      - "8502:8502"
      - "5055:5055"
    environment:
      - ANTHROPIC_API_KEY=your_key_here
      - API_URL=http://192.168.1.100:5055  # ← Important!
      - SURREAL_URL=ws://localhost:8000/rpc
      - SURREAL_USER=root
      - SURREAL_PASSWORD=root
      - SURREAL_NAMESPACE=racing_tips
      - SURREAL_DATABASE=production
    volumes:
      - ./notebook_data:/app/data
      - ./surreal_data:/mydata
    restart: always
```

**Access from any device on your network**: http://192.168.1.100:8502

---

## Reverse Proxy Deployment (Advanced)

If you want to use a custom domain with HTTPS:

### Example: Nginx reverse proxy

1. **Update docker-compose.yml**:

```yaml
services:
  racing_tips_notebook:
    image: domocarroll/racing-tips-notebook:latest
    ports:
      - "127.0.0.1:8502:8502"  # Only expose locally
      - "127.0.0.1:5055:5055"  # Only expose locally
    environment:
      - ANTHROPIC_API_KEY=your_key_here
      - API_URL=https://racing-tips.yourdomain.com  # Your public domain
      - SURREAL_URL=ws://localhost:8000/rpc
      - SURREAL_USER=root
      - SURREAL_PASSWORD=root
      - SURREAL_NAMESPACE=racing_tips
      - SURREAL_DATABASE=production
    volumes:
      - ./notebook_data:/app/data
      - ./surreal_data:/mydata
    restart: always
```

2. **Configure Nginx** (`/etc/nginx/sites-available/racing-tips`):

```nginx
server {
    listen 80;
    server_name racing-tips.yourdomain.com;
    
    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name racing-tips.yourdomain.com;
    
    # SSL configuration (use certbot to generate)
    ssl_certificate /etc/letsencrypt/live/racing-tips.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/racing-tips.yourdomain.com/privkey.pem;
    
    # Frontend
    location / {
        proxy_pass http://localhost:8502;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    # API
    location /api/ {
        proxy_pass http://localhost:5055/api/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        
        # Increase timeouts for OCR processing
        proxy_read_timeout 600s;
        proxy_connect_timeout 600s;
        proxy_send_timeout 600s;
    }
}
```

3. **Enable the site**:

```bash
sudo ln -s /etc/nginx/sites-available/racing-tips /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

4. **Get SSL certificate**:

```bash
sudo certbot --nginx -d racing-tips.yourdomain.com
```

**Access**: https://racing-tips.yourdomain.com

---

## Environment Variables Reference

### Required

| Variable | Description | Example |
|----------|-------------|---------|
| `ANTHROPIC_API_KEY` | Anthropic API key (recommended for OCR) | `sk-ant-...` |
| `OPENAI_API_KEY` | OpenAI API key (alternative) | `sk-...` |

### Optional

| Variable | Description | Default |
|----------|-------------|---------|
| `API_URL` | Public URL for API access | Auto-detected |
| `SURREAL_URL` | SurrealDB connection URL | `ws://localhost:8000/rpc` |
| `SURREAL_USER` | SurrealDB username | `root` |
| `SURREAL_PASSWORD` | SurrealDB password | `root` |
| `SURREAL_NAMESPACE` | SurrealDB namespace | `racing_tips` |
| `SURREAL_DATABASE` | SurrealDB database name | `production` |
| `OPEN_NOTEBOOK_PASSWORD` | Password protect the instance | (none) |
| `API_CLIENT_TIMEOUT` | API timeout in seconds | `300` |
| `GOOGLE_API_KEY` | Google Gemini API key | (none) |
| `FIRECRAWL_API_KEY` | Firecrawl API key for web scraping | (none) |
| `JINA_API_KEY` | Jina API key for document processing | (none) |

---

## Data Persistence

Racing Tips Notebook stores data in two volumes:

### 1. Application Data (`./notebook_data`)

Contains:
- Uploaded racing tip sheets (images, PDFs)
- Processed OCR results
- Generated embeddings
- User-created notebooks and notes

**Backup**: Regularly backup this directory to preserve your racing tips data.

### 2. Database Data (`./surreal_data`)

Contains:
- SurrealDB database files
- All structured data (notebooks, sources, metadata)

**Backup**: Backup this directory to preserve your database.

### Backup Strategy

```bash
# Stop the container
docker compose down

# Backup both directories
tar -czf racing-tips-backup-$(date +%Y%m%d).tar.gz notebook_data surreal_data

# Restart the container
docker compose up -d
```

### Restore from Backup

```bash
# Stop the container
docker compose down

# Extract backup
tar -xzf racing-tips-backup-20251024.tar.gz

# Restart the container
docker compose up -d
```

---

## Upgrading

### Upgrade to Latest Version

```bash
# Pull the latest image
docker compose pull

# Restart with new image
docker compose up -d
```

Your data will be preserved in the volumes.

### Upgrade from Open Notebook

If you're migrating from the original Open Notebook:

1. **Backup your data** (see above)
2. **Stop the old container**: `docker compose down`
3. **Update docker-compose.yml** to use `domocarroll/racing-tips-notebook:latest`
4. **Update environment variables** (change namespace to `racing_tips`)
5. **Start the new container**: `docker compose up -d`

Your existing notebooks and sources will work with Racing Tips Notebook.

---

## Troubleshooting

### Container won't start

**Check logs**:
```bash
docker compose logs -f
```

**Common issues**:
- Missing API key: Add `ANTHROPIC_API_KEY` or `OPENAI_API_KEY`
- Port conflict: Change ports in docker-compose.yml
- Permission issues: Ensure directories are writable

### Can't access from other devices

**Problem**: Works on localhost but not from other computers

**Solution**: Set `API_URL` to your server's IP address:
```yaml
environment:
  - API_URL=http://192.168.1.100:5055
```

### OCR not working

**Problem**: Uploaded tip sheets but no tips extracted

**Solutions**:
1. Verify API key is set correctly
2. Check API provider has credits/quota
3. Try a different AI provider (Anthropic, OpenAI, Gemini)
4. Check image quality (should be clear and readable)

### Slow performance

**Problem**: AI responses are slow or timing out

**Solutions**:
1. Increase timeout: `API_CLIENT_TIMEOUT=600`
2. Use faster AI provider (Anthropic Claude or OpenAI GPT-4)
3. Ensure good network connection to AI provider
4. Check server resources (CPU, RAM, disk)

### Database errors

**Problem**: Database connection errors or data corruption

**Solutions**:
1. Check SurrealDB is running: `docker compose ps`
2. Verify database credentials in environment variables
3. Restore from backup if data is corrupted
4. Check disk space: `df -h`

---

## Security Considerations

### Password Protection

Add password protection for public deployments:

```yaml
environment:
  - OPEN_NOTEBOOK_PASSWORD=your_secure_password
```

Users will need to enter this password to access the application.

### Network Security

For production deployments:

1. **Use HTTPS**: Set up reverse proxy with SSL/TLS
2. **Firewall**: Only expose necessary ports (80, 443)
3. **Strong passwords**: Use strong database passwords
4. **Regular updates**: Keep Docker images up to date

### Data Privacy

Racing Tips Notebook is designed for self-hosting:

- All data stays on your server
- No telemetry or tracking
- API keys are only used for AI providers you choose
- Use Ollama for 100% local processing (no cloud APIs)

---

## Performance Tuning

### For Large Deployments

If you're processing many tip sheets daily:

1. **Increase resources** in docker-compose.yml:

```yaml
services:
  racing_tips_notebook:
    # ... other config ...
    deploy:
      resources:
        limits:
          cpus: '4'
          memory: 8G
        reservations:
          cpus: '2'
          memory: 4G
```

2. **Use faster AI provider**: Anthropic Claude or OpenAI GPT-4 Turbo

3. **Optimize database**: Regular backups and cleanup of old data

4. **Use SSD storage**: Store volumes on fast SSD drives

---

## Support

- **GitHub Issues**: https://github.com/domocarroll/racing-tips-notebook/issues
- **Documentation**: See RACING_TIPS_GUIDE.md for usage instructions
- **Original Project**: Built on [Open Notebook](https://github.com/lfnovo/open-notebook)

---

## License

Racing Tips Notebook is licensed under the MIT License. See LICENSE file for details.

