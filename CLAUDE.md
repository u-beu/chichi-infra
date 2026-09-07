# chichi-infra Root Guidelines

## Overview
Multi-container architecture managed by Docker Compose.
External traffic routes via Cloudflare Tunnel through Nginx Reverse Proxy to internal services.
Web-to-Bot requests use Redis Pub/Sub, while Bot-to-Web metadata synchronization uses REST APIs.

## Services & Submodules
- `nginx`: Front reverse proxy receiving Cloudflare Tunnel traffic
- `chichi`: Spring Boot Web Server (SSR with Thymeleaf & REST APIs)
- `chichi-bot`: Python Discord Bot & AI Modules (bot, api, worker)
- `redis`: Message Broker & Caching

## Network & Traffic Flow
- External Traffic -> Cloudflare Tunnel -> Nginx (Port 80/443) -> `chichi` / `chichi-bot-api`
- Web -> Bot (Play Request): Redis Pub/Sub (`playback` channel)
- Bot -> Web (Metadata Sync): Direct HTTP REST API call to `chichi`

## Core Commands
- Infrastructure Up: `docker-compose up -d`
- Infrastructure Down: `docker-compose down`
- View Logs: `docker-compose logs -f [service_name]`

## Message Broker Contract (Redis Pub/Sub)
- Channel: `playback`
- Publisher: `chichi` (Spring)
- Subscriber: `chichi-bot` (Python)
- JSON Payload Format:
  {
  "action": "ACTION_PLAYBACK",
  "guild_id": "string",
  "user_id": "string",
  "query": "string"
  }

## General Rules
- Keep changes scoped to submodules.
- Ensure Docker environment variables (`.env`) and Cloudflare routing context stay synchronized.
- Concise responses only.