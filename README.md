# Racing Tips Notebook

[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![MIT License][license-shield]][license-url]

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/domocarroll/racing-tips-notebook">
    <img src="docs/assets/hero.svg" alt="Logo">
  </a>

  <h3 align="center">Racing Tips Notebook</h3>

  <p align="center">
    An AI-powered system for aggregating and analyzing horse racing tips!
    <br />
    Built on Open Notebook - the open source alternative to Google's Notebook LM
    <br />
    <br />
    <a href="docs/getting-started/index.md">📚 Get Started</a>
    ·
    <a href="docs/user-guide/index.md">📖 User Guide</a>
    ·
    <a href="docs/features/index.md">✨ Features</a>
    ·
    <a href="docs/deployment/index.md">🚀 Deploy</a>
  </p>
</div>

## A private, AI-powered racing tips aggregation and analysis platform

![Racing Tips Notebook](docs/assets/asset_list.png)

**Racing Tips Notebook** combines the power of AI with horse racing expertise to help you aggregate, analyze, and make sense of racing tips from multiple sources.

**Racing Tips Notebook empowers you to:**
- 🏇 **Aggregate racing tips** - Collect tips from newspaper images, PDFs, and other sources
- 🤖 **AI-powered extraction** - Automatically extract tipster selections using vision AI
- 📊 **Expert consensus** - See which horses are most popular across multiple tipsters
- 🔍 **Intelligent search** - Query your tips database with natural language
- 💬 **Chat with your data** - Ask questions about tips, form, and patterns
- 📈 **Track performance** - Analyze tipster accuracy over time
- 🔒 **Keep it private** - Self-hosted solution, your data stays with you
- 🤖 **Choose your AI** - Support for 16+ providers including OpenAI, Anthropic, Ollama, and more

---

## 🚀 Quick Start

**Docker Images Available:**
- **Docker Hub**: `domocarroll/racing-tips-notebook:latest`
- **GitHub Container Registry**: `ghcr.io/domocarroll/racing-tips-notebook:latest`

### Choose Your Setup:

#### 🏠 Local Machine Setup
Perfect if Docker runs on the **same computer** where you'll access Racing Tips Notebook.

```shell
mkdir racing-tips-notebook && cd racing-tips-notebook

docker run -d \
  --name racing-tips-notebook \
  -p 8502:8502 -p 5055:5055 \
  -v ./notebook_data:/app/data \
  -v ./surreal_data:/mydata \
  -e ANTHROPIC_API_KEY=your_key_here \
  -e SURREAL_URL="ws://localhost:8000/rpc" \
  -e SURREAL_USER="root" \
  -e SURREAL_PASSWORD="root" \
  -e SURREAL_NAMESPACE="racing_tips" \
  -e SURREAL_DATABASE="production" \
  domocarroll/racing-tips-notebook:latest
```

**Access at:** http://localhost:8502

#### 🌐 Remote Server Setup
Use this for servers, Raspberry Pi, NAS, Proxmox, or any remote machine.

```shell
mkdir racing-tips-notebook && cd racing-tips-notebook

docker run -d \
  --name racing-tips-notebook \
  -p 8502:8502 -p 5055:5055 \
  -v ./notebook_data:/app/data \
  -v ./surreal_data:/mydata \
  -e ANTHROPIC_API_KEY=your_key_here \
  -e API_URL=http://YOUR_SERVER_IP:5055 \
  -e SURREAL_URL="ws://localhost:8000/rpc" \
  -e SURREAL_USER="root" \
  -e SURREAL_PASSWORD="root" \
  -e SURREAL_NAMESPACE="racing_tips" \
  -e SURREAL_DATABASE="production" \
  domocarroll/racing-tips-notebook:latest
```

**Replace `YOUR_SERVER_IP`** with your server's IP (e.g., `192.168.1.100`) or domain

**Access at:** http://YOUR_SERVER_IP:8502

> **⚠️ Critical Setup Notes:**
> 
> **Both ports are required:**
> - **Port 8502**: Web interface (what you see in your browser)
> - **Port 5055**: API backend (required for the app to function)
> 
> **API_URL must match how YOU access the server:**
> - ✅ Access via `http://192.168.1.100:8502` → set `API_URL=http://192.168.1.100:5055`
> - ✅ Access via `http://myserver.local:8502` → set `API_URL=http://myserver.local:5055`
> - ❌ Don't use `localhost` for remote servers - it won't work from other devices!

### Using Docker Compose (Recommended for Easy Management)

Create a `docker-compose.yml` file:

```yaml
services:
  racing_tips_notebook:
    image: domocarroll/racing-tips-notebook:latest
    ports:
      - "8502:8502"  # Web UI
      - "5055:5055"  # API (required!)
    environment:
      - ANTHROPIC_API_KEY=your_key_here
      # For remote access, uncomment and set your server IP/domain:
      # - API_URL=http://192.168.1.100:5055
      # Database connection (required for single-container)
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

Start with: `docker compose up -d`

---

## 🏇 Racing Tips Features

### Upload Racing Tip Sheets
- Upload newspaper tip sheets as images or PDFs
- Automatic OCR extraction of tipster columns
- Support for multiple page formats and layouts
- Extract race numbers, horse names, and positions

### Expert Consensus Analysis
- Aggregate tips across all tipsters
- See which horses are most popular for 1st, 2nd, 3rd
- Calculate consensus scores and confidence levels
- Identify "best bets" and popular selections

### Natural Language Queries
- "Show me all tips for Flemington on Saturday"
- "Which horses have the most tips for 1st place?"
- "Compare tipsters for race 3"
- "What are the best bets for today?"

### Performance Tracking
- Track tipster accuracy over time
- Analyze which tipsters perform best
- Compare tips against actual results
- Generate performance reports

### AI-Powered Chat
- Chat with your racing tips database
- Ask questions about form, patterns, and trends
- Get AI-generated insights and recommendations
- Query historical data with natural language

---

## 🆘 Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| **"Unable to connect to server"** | Set `API_URL` environment variable to match how you access the server (see remote setup above) |
| **Blank page or errors** | Ensure BOTH ports (8502 and 5055) are exposed in your docker command |
| **Works on server but not from other computers** | Don't use `localhost` in `API_URL` - use your server's actual IP address |
| **"404" or "config endpoint" errors** | Don't add `/api` to `API_URL` - use just `http://your-ip:5055` |
| **OCR not working** | Ensure you have set `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` environment variable |

---

## 🛠️ Built With

This project is built on [Open Notebook](https://github.com/lfnovo/open-notebook), an open source alternative to Google's Notebook LM.

**Technology Stack:**
- **Frontend**: Next.js 15 + React 19 + TypeScript
- **Backend**: FastAPI (Python)
- **Database**: SurrealDB
- **AI**: Support for 16+ providers (Anthropic, OpenAI, Ollama, etc.)
- **Deployment**: Docker

---

## 📝 License

Distributed under the MIT License. See `LICENSE` for more information.

---

## 🙏 Acknowledgments

- Built on [Open Notebook](https://github.com/lfnovo/open-notebook) by [lfnovo](https://github.com/lfnovo)
- Inspired by Google's Notebook LM
- Racing tips extraction powered by AI vision models

---

<!-- MARKDOWN LINKS & IMAGES -->
[contributors-shield]: https://img.shields.io/github/contributors/domocarroll/racing-tips-notebook.svg?style=for-the-badge
[contributors-url]: https://github.com/domocarroll/racing-tips-notebook/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/domocarroll/racing-tips-notebook.svg?style=for-the-badge
[forks-url]: https://github.com/domocarroll/racing-tips-notebook/network/members
[stars-shield]: https://img.shields.io/github/stars/domocarroll/racing-tips-notebook.svg?style=for-the-badge
[stars-url]: https://github.com/domocarroll/racing-tips-notebook/stargazers
[issues-shield]: https://img.shields.io/github/issues/domocarroll/racing-tips-notebook.svg?style=for-the-badge
[issues-url]: https://github.com/domocarroll/racing-tips-notebook/issues
[license-shield]: https://img.shields.io/github/license/domocarroll/racing-tips-notebook.svg?style=for-the-badge
[license-url]: https://github.com/domocarroll/racing-tips-notebook/blob/main/LICENSE
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/linkedin_username

