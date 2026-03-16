# AI Toolkit

A complete privacy-focused Docker Compose setup for running AI tools locally. No external APIs required - everything runs on your own hardware with 100% privacy.

## About This Project

This project is based on the [Open Notebook Docker Compose examples](https://github.com/lfnovo/open-notebook/blob/main/examples/README.md) with customizations and improvements for easier local deployment.

The current setup includes core AI infrastructure services (Open Notebook, Ollama, Speaches, SurrealDB) plus experimental tools (OpenPlaud). More AI tools will be added to this Docker Compose setup for a comprehensive quick-start AI toolkit.

## Overview

AI Toolkit provides a complete local AI infrastructure with LLMs, embeddings, text-to-speech, speech-to-text, and a powerful research assistant - all running in Docker containers. Perfect for offline work, air-gapped environments, or anyone who values data privacy.

## Quick Start

```bash
# 1. Clone this repository
git clone <repository-url>
cd ai-toolkit

# 2. Copy and configure environment variables
cp .env.example .env
# Edit .env and set your OPEN_NOTEBOOK_ENCRYPTION_KEY and other values

# 3. Start all services
docker compose up -d

# 4. Pull required models (see Models section below)
```

## Included Services

### Open Notebook

**Purpose**: Your personal AI research assistant

**What it does**:
- Research and document analysis
- Chat with your documents and notes
- Knowledge base management
- Note-taking with AI assistance
- Web content extraction and summarization

**Access**: http://localhost:8502

**Configuration**:
- Requires SurrealDB for data storage
- Connects to Ollama for LLM inference
- Connects to Speaches for voice features
- Encryption key must be set in environment variables

---

### Ollama

**Purpose**: Local LLM inference server

**What it does**:
- Run Large Language Models (LLMs) locally
- Generate embeddings for semantic search
- No API keys or internet required for inference
- Supports various open-source models (Llama, Mistral, Qwen, etc.)

**Access**: http://localhost:11434

**Suggested Models**:

| Model             | Type       | Use Case                        |
|-------------------|------------|---------------------------------|
| mistral           | LLM        | Fast, good general purpose (7B) |
| llama3.1          | LLM        | Balanced performance (8B)       |
| qwen2.5           | LLM        | Multilingual support            |
| nomic-embed-text  | Embeddings | Text embeddings (137M)          |
| mxbai-embed-large | Embeddings | High-quality embeddings (334M)  |

See [Installing Models](#installing-models) section below for installation commands.

**GPU Support**: Uncomment the GPU section in docker-compose.yml for NVIDIA GPU acceleration

---

### Speaches

**Purpose**: Local speech synthesis and recognition

**What it does**:
- **Text-to-Speech (TTS)**: Convert text to natural-sounding speech
- **Speech-to-Text (STT)**: Transcribe audio to text
- OpenAI-compatible API
- No cloud services or API costs

**Access**: http://localhost:8969

**Suggested Models**:

| Model                   | Type | Description                 |
|-------------------------|------|-----------------------------|
| Kokoro-82M-v1.0-ONNX    | TTS  | High-quality text-to-speech |
| faster-whisper-small    | STT  | Balanced speed/accuracy     |
| faster-whisper-base     | STT  | Fast transcription          |
| faster-whisper-large-v3 | STT  | Best accuracy               |

See [Installing Models](#installing-models) section below for installation commands.

**GPU Support**: Change image tag to `latest-cuda` and add GPU device mapping for NVIDIA acceleration

---

### SurrealDB

**Purpose**: Local database for Open Notebook

**What it does**:
- Stores notes, documents, and chat history
- Graph database with SQL-like querying
- Experimental GraphQL support
- Persistent storage via Docker volume

**Access**: http://localhost:8000

**Default Credentials**:
- User: `root`
- Password: `root`

---

### OpenPlaud (Experimental)

> ⚠️ **Experimental**: This service is currently under testing and may be removed in the future if it doesn't meet requirements or proves insufficient (currently experiencing timeouts when processing large recordings).

**Purpose**: Self-hosted AI transcription interface for Plaud Note devices

**What it does**:
- Replace Plaud's $20/month AI subscription with your own OpenAI-compatible API keys
- Transcribe recordings from Plaud Note devices
- Support for multiple AI providers (OpenAI, Groq, Together AI, OpenRouter, local models)
- Browser-based transcription with Transformers.js (zero API costs)
- Auto-sync recordings from Plaud devices
- Multiple export formats (JSON, TXT, SRT, VTT)

**Access**: http://localhost:3000

**Configuration**:
- Requires PostgreSQL database
- Connects to OpenAI-compatible APIs for transcription
- Encrypted credentials storage with AES-256-GCM

**Repository**: [OpenPlaud on GitHub](https://github.com/openplaud/openplaud)

---

### PostgreSQL

**Purpose**: Relational database for OpenPlaud

**What it does**:
- Stores user accounts, recordings metadata, and transcriptions
- PostgreSQL 16 with Alpine Linux (lightweight)
- Persistent storage via Docker volume

**Access**: `localhost:5432`

**Default Credentials**:
- User: `postgres`
- Password: `postgres`
- Database: `openplaud`

**Connection**:
```bash
# Connect via psql
docker compose exec postgresdb psql -U postgres -d openplaud
```

---

### Cloudflared (Optional)

**Purpose**: Secure tunnel for remote access

**What it does**:
- Expose local services to the internet securely
- No public IP or port forwarding required
- Uses Cloudflare's network for security

**Setup**:
1. Create a Cloudflare Tunnel via the Cloudflare dashboard. See [Cloudflare Tunnel documentation](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/) for detailed instructions
2. Copy the tunnel token to `.env` file as `TUNNEL_TOKEN`
3. Restart the cloudflared service

## System Requirements

### Minimum (CPU Only)
- 8 GB RAM
- 20 GB disk space
- 4 CPU cores

### Recommended (with GPU)
- 16+ GB RAM
- 8+ GB VRAM (NVIDIA GPU)
- 50 GB disk space
- 8+ CPU cores

## Configuration

### Environment Variables

Copy `.env.example` to `.env` and configure the following variables:

#### Required

| Variable                       | Description                                   | Example                                                            |
|--------------------------------|-----------------------------------------------|--------------------------------------------------------------------|
| `OPEN_NOTEBOOK_ENCRYPTION_KEY` | Secret key for encrypting Open Notebook data. | `76&kOnqFN7jP6jHeOKfvzEi+ssNJ^za2EkaGQB+z9B0sPflEgbx0XI3HIHap%tbV` |

#### Optional

| Variable                 | Description                                                                                                                                                                      | Example                   |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| `TUNNEL_TOKEN`           | Cloudflare tunnel token for remote access via Cloudflared. See [Cloudflare Tunnel docs](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/) | `eyJhIjoiY2JhN...`        |
| `FIRECRAWL_API_KEY`      | API key for Firecrawl web scraping service (enables automatic web content extraction in Open Notebook)                                                                           | `fc-xxxxxxxxxxxxx`        |
| `API_URL`                | Custom API URL for Open Notebook (advanced use)                                                                                                                                  | `https://api.example.com` |
| `OPEN_NOTEBOOK_PASSWORD` | Password protection for Open Notebook UI                                                                                                                                         | `your-secure-password`    |

#### Experimental

| Variable               | Description                                                        | Example                 |
|------------------------|--------------------------------------------------------------------|-------------------------|
| `DEFAULT_STORAGE_TYPE` | Storage backend for OpenPlaud (`local` or `s3`)                    | `local`                 |
| `ENCRYPTION_KEY`       | AES-256-GCM encryption key for credentials (generate with openssl) | `openssl rand -hex 32`  |
| `BETTER_AUTH_SECRET`   | Authentication secret key (generate with openssl)                  | `openssl rand -hex 32`  |
| `OPEN_PLAUD_APP_URL`   | Public URL for OpenPlaud app                                       | `http://localhost:3000` |

> ⚠️ **Note**: OpenPlaud is experimental. Generate unique keys for `ENCRYPTION_KEY` and `BETTER_AUTH_SECRET` using `openssl rand -hex 32`.

**Important**: Never commit your `.env` file to version control. It contains sensitive credentials.

### Open Notebook Setup

1. **Configure Ollama**:
   - Go to Settings → API Keys
   - Add Credential → Select "Ollama"
   - Base URL: `http://ollama:11434`
   - Test Connection → Discover Models → Register Models

2. **Configure Speaches**:
   - Go to Settings → API Keys
   - Add Credential → Select "OpenAI-Compatible"
   - Name: "Local Speaches"
   - Base URL: `http://host.docker.internal:8969/v1` (macOS/Windows)
   - Test Connection and Discover Models

### OpenPlaud Setup (Experimental)

1. **Generate encryption keys**:
   ```bash
   # Generate BETTER_AUTH_SECRET
   openssl rand -hex 32

   # Generate ENCRYPTION_KEY
   openssl rand -hex 32
   ```

2. **Configure environment variables**:
   - Copy the generated keys to your `.env` file:
     - `BETTER_AUTH_SECRET` - Authentication secret
     - `ENCRYPTION_KEY` - AES-256-GCM encryption for API keys
     - `OPEN_PLAUD_APP_URL` - Set to `http://localhost:3000` (or your tunnel URL)

3. **Restart OpenPlaud services**:
   ```bash
   docker compose up -d postgresdb open_plaud
   ```

4. **Create an account**:
   - Open http://localhost:3000 in your browser
   - Follow the onboarding wizard to create your account
   - No email verification required for local setup

5. **Configure AI provider**:
   - Go to Settings → AI Providers
   - Add your OpenAI-compatible API (OpenAI, Groq, Together AI, etc.)
   - For local models: use your Ollama endpoint with an OpenAI-compatible wrapper

6. **Connect Plaud account** (optional):
   - Go to Settings → Sync
   - Enter your Plaud.ai credentials to auto-sync recordings
   - Recordings will be downloaded automatically based on your sync interval

7. **Test transcription**:
   - Upload a test recording or use auto-sync
   - Select your configured AI provider
   - Verify transcription completes successfully

## Project Structure

```
ai-toolkit/
├── docker-compose.yml      # Main compose file with all services
├── .env.example            # Example environment variables
├── .env                    # Your configuration (gitignored)
├── notebook_data/          # Open Notebook persistent data
├── surreal_data/           # SurrealDB persistent data
├── openplaud/              # OpenPlaud submodule (experimental)
└── openplaud_data/         # OpenPlaud PostgreSQL data (experimental)
```

## Installing Models

After starting the services, you need to download AI models using `docker compose exec`:

### Ollama Models

Use `docker compose exec <container-name> ollama pull <model-name>` to download LLMs and embedding models:

```bash
# LLM Models - General purpose
docker compose exec <container-name> ollama pull mistral
docker compose exec <container-name> ollama pull llama3.1
docker compose exec <container-name> ollama pull qwen2.5

# Embedding Models - For vector search
docker compose exec <container-name> ollama pull nomic-embed-text
docker compose exec <container-name> ollama pull mxbai-embed-large

# List installed models
docker compose exec <container-name> ollama list

# Remove a model
docker compose exec <container-name> ollama rm <model-name>
```

### Speaches Models

Use `docker compose exec <container-name> uv tool run speaches-cli model download <model-name>` for speech models:

```bash
# Text-to-Speech (TTS)
docker compose exec <container-name> uv tool run speaches-cli model download speaches-ai/Kokoro-82M-v1.0-ONNX

# Speech-to-Text (STT) - Choose based on your needs
docker compose exec <container-name> uv tool run speaches-cli model download Systran/faster-whisper-small
docker compose exec <container-name> uv tool run speaches-cli model download Systran/faster-whisper-large-v3

# List installed models
docker compose exec <container-name> uv tool run speaches-cli model ls

# List available models:
docker compose exec <container-name> uv tool run speaches-cli registry ls
```

### Why Use `docker compose exec`?

- **Runs inside the container**: Commands execute within the running container with access to its volumes and configuration
- **Access to downloaded models**: Models are saved to Docker volumes and persist between restarts
- **Consistent environment**: Uses the container's environment and dependencies

## Commands

```bash
# Start all services
docker compose up -d

# Stop all services
docker compose down

# Update all images to latest version (pull new images, then recreate containers)
docker compose pull
docker compose up -d

# View logs
docker compose logs -f

# View specific service logs
docker compose logs -f open_notebook

# Pull new model
docker exec ai-toolkit-ollama-1 ollama pull <model-name>

# Restart a service
docker compose restart <service-name>
```

## Cost Comparison

| Feature    | Local (This Setup) | Cloud (OpenAI + ElevenLabs) |
|------------|--------------------|-----------------------------|
| LLM        | $0                 | ~$0.01-0.10 per 1K tokens   |
| Embeddings | $0                 | ~$0.0001 per 1K tokens      |
| TTS        | $0                 | ~$0.015 per minute          |
| STT        | $0                 | ~$0.006 per minute          |
| Privacy    | 100% private       | Data sent to providers      |

## Troubleshooting

### Models Not Available
Ensure you've pulled the models:
```bash
docker exec ai-toolkit-ollama-1 ollama list
docker compose exec speaches uv tool run speaches-cli model list
```

### Out of Memory
- Use smaller models (e.g., qwen2.5 7B instead of 70B)
- Reduce batch size in environment variables
- Add swap space

### GPU Not Detected
- Ensure NVIDIA Docker runtime is installed
- Uncomment GPU sections in docker-compose.yml
- For Speaches, use the `latest-cuda` image tag

## Documentation

### Open Notebook Setup Guides

- [Ollama Setup Guide](https://github.com/lfnovo/open-notebook/blob/main/examples/README.md) - Complete guide for local LLM setup with Ollama
- [Local TTS Setup](https://github.com/lfnovo/open-notebook/blob/main/docs/5-CONFIGURATION/local-tts.md) - Configure text-to-speech with Speaches
- [Local STT Setup](https://github.com/lfnovo/open-notebook/blob/main/docs/5-CONFIGURATION/local-stt.md) - Configure speech-to-text with Speaches

### Official Documentation

- [Open Notebook Documentation](https://github.com/lfnovo/open-notebook)
- [Ollama Documentation](https://github.com/ollama/ollama)
- [Speaches Documentation](https://github.com/speaches-ai/speaches)
- [SurrealDB Documentation](https://surrealdb.com/docs)
- [OpenPlaud Documentation](https://github.com/openplaud/openplaud)

## License

This project is provided as-is for local AI development and research purposes.
