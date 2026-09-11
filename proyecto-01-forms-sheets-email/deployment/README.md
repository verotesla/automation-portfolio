# Local n8n Deployment

This folder contains a safe example Docker Compose configuration for running n8n locally.

## Files

- `docker-compose.example.yml`
- `.env.example`

## Usage

1. Copy both files into your local n8n working directory.
2. Rename:

```text
docker-compose.example.yml → docker-compose.yml
.env.example → .env
```

3. Replace the placeholder encryption key in `.env`.
4. Start n8n:

```bash
docker compose up -d
```

5. Open:

```text
http://localhost:5678
```

## Important

Do not commit your real `.env` file or encryption key.

The Docker volume keeps n8n application data persistent across container recreation.
