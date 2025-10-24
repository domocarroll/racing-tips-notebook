# Racing Tips Notebook - Quick Start

Get up and running with Racing Tips Notebook in 5 minutes!

## Prerequisites

- Docker installed on your computer
- An Anthropic API key (recommended) or OpenAI API key
  - Get Anthropic key: https://console.anthropic.com/
  - Get OpenAI key: https://platform.openai.com/

## Installation

### Step 1: Create a directory

```bash
mkdir racing-tips-notebook
cd racing-tips-notebook
```

### Step 2: Create docker-compose.yml

Create a file named `docker-compose.yml` with this content:

```yaml
services:
  racing_tips_notebook:
    image: domocarroll/racing-tips-notebook:latest
    ports:
      - "8502:8502"
      - "5055:5055"
    environment:
      - ANTHROPIC_API_KEY=your_anthropic_key_here
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

**Important**: Replace `your_anthropic_key_here` with your actual API key!

### Step 3: Start the container

```bash
docker compose up -d
```

This will:
- Download the Racing Tips Notebook image (~2GB)
- Start all services (database, API, frontend)
- Create data directories for persistence

### Step 4: Access the application

Open your browser and go to: **http://localhost:8502**

You should see the Racing Tips Notebook interface!

---

## First Steps

### 1. Create Your First Notebook

1. Click **"Notebooks"** in the sidebar
2. Click **"+ New Notebook"**
3. Name it: "My First Racing Tips"
4. Click **"Create"**

### 2. Upload a Racing Tip Sheet

1. Click **"Sources"** in the sidebar
2. Click **"+ Add Source"**
3. Choose **"File Upload"**
4. Select a racing tip sheet image (JPG, PNG) or PDF
5. Wait for AI to extract the tips (this may take 30-60 seconds)

### 3. View Extracted Tips

1. Click on the uploaded source to view details
2. You'll see the extracted tipster selections
3. The AI has parsed race numbers, horse names, and positions

### 4. Link Tips to Your Notebook

1. On the source detail page, click **"Add to Notebook"**
2. Select "My First Racing Tips"
3. The tips are now organized in your notebook

### 5. Query Your Tips

1. Click **"Search"** in the sidebar
2. Try a query like: "Show me all tips for race 1"
3. The AI will search your tips and provide results

### 6. Chat with Your Data

1. Open your notebook ("My First Racing Tips")
2. Click the **"Chat"** tab
3. Ask: "Which horse has the most tips for 1st place?"
4. The AI will analyze your tips and respond

---

## What's Next?

- **Upload more tip sheets**: Build your tips database
- **Create notebooks by meeting**: Organize tips by venue and date
- **Track tipster performance**: Compare tips against actual results
- **Generate insights**: Use AI to analyze patterns and trends
- **Read the full guide**: See RACING_TIPS_GUIDE.md for detailed instructions

---

## Common Issues

### "Unable to connect to server"

**Problem**: Can't access http://localhost:8502

**Solution**: Wait 1-2 minutes for all services to start. Check status:
```bash
docker compose ps
```

All services should show "Up" status.

### "API key not found"

**Problem**: OCR extraction fails with API key error

**Solution**: Make sure you replaced `your_anthropic_key_here` in docker-compose.yml with your actual API key. Then restart:
```bash
docker compose down
docker compose up -d
```

### "Port already in use"

**Problem**: Error about port 8502 or 5055 already in use

**Solution**: Change the ports in docker-compose.yml:
```yaml
ports:
  - "8503:8502"  # Use 8503 instead
  - "5056:5055"  # Use 5056 instead
```

Then access at http://localhost:8503

### OCR not extracting tips

**Problem**: Uploaded tip sheet but no tips extracted

**Solutions**:
1. Check image quality (should be clear and readable)
2. Ensure tip sheet has a standard columnar format
3. Try uploading as PDF instead of image
4. Verify your API key has credits/quota remaining

---

## Stopping and Restarting

### Stop the container

```bash
docker compose down
```

### Restart the container

```bash
docker compose up -d
```

### View logs

```bash
docker compose logs -f
```

Press Ctrl+C to exit logs.

---

## Remote Access

Want to access from other devices on your network?

1. Find your server's IP address:
   ```bash
   # On Linux/Mac
   hostname -I
   
   # On Windows
   ipconfig
   ```

2. Update docker-compose.yml:
   ```yaml
   environment:
     - ANTHROPIC_API_KEY=your_key_here
     - API_URL=http://YOUR_SERVER_IP:5055  # Add this line
     # ... rest of config
   ```

3. Restart:
   ```bash
   docker compose down
   docker compose up -d
   ```

4. Access from any device: http://YOUR_SERVER_IP:8502

---

## Backup Your Data

Your racing tips are stored in two directories:

- `./notebook_data` - Uploaded tip sheets and processed data
- `./surreal_data` - Database files

**To backup**:
```bash
tar -czf racing-tips-backup.tar.gz notebook_data surreal_data
```

**To restore**:
```bash
tar -xzf racing-tips-backup.tar.gz
```

---

## Getting Help

- **Full User Guide**: RACING_TIPS_GUIDE.md
- **Deployment Guide**: DEPLOYMENT.md
- **GitHub Issues**: https://github.com/domocarroll/racing-tips-notebook/issues

---

## Tips for Best Results

1. **Use high-quality images**: 300 DPI or higher for best OCR results
2. **Organize by meeting**: Create one notebook per race meeting
3. **Consistent naming**: Use "Venue - Date" format for notebooks
4. **Regular backups**: Backup your data weekly
5. **Try different queries**: Experiment with natural language questions

---

Happy racing! 🏇

