# chichi-infra Root Guidelines

## Directory Structure
chichi-project/
├── chichi-infra/  <-- Root context for infrastructure commands & compose
├── chichi/        <-- Spring Boot 소스 코드 (`../chichi` 참조)
└── chichi-bot/    <-- Python 봇 소스 코드 (`../chichi-bot` 참조)

## Overview
- Docker Compose 기반 Multi-container architecture
- External Traffic: Cloudflare Tunnel -> Nginx Reverse proxy -> 내부 Web Service 라우팅
- Web-Bot Communication: 전용 API Server 없이 Redis Message Broker 기반 비동기 통신

## Services & Submodules
- `nginx`: Cloudflare Tunnel 트래픽 수신 및 Web Service 전달 Reverse proxy
- `chichi`: Spring Boot 웹 서버 (Thymeleaf 기반 SSR 및 Business logic)
- `chichi-bot`: Python Discord bot 및 AI/Audio Worker 모듈
- `redis`: Message Broker 및 Caching (8GB RAM 제약 고려한 메모리 경계 준수 필요)

## Network & Traffic Flow
- **External Traffic:** 외부 -> Cloudflare Tunnel -> Nginx (Port 80/443) -> `chichi`
- **Web ↔ Bot Communication:** Redis Message Broker (Queue 및 Pub/Sub) 전용 사용 (직접 HTTP API 통신 금지)

## Core Commands
- 인프라 실행: `docker-compose up -d`
- 인프라 중지: `docker-compose down`
- 로그 조회: `docker-compose logs -f [service_name]`

## Message Broker Contract (Redis)
- **Mechanism:** Redis 기반 Message Queue 및 Pub/Sub
- **JSON Payload Format:**
  {
    "action": "ACTION_PLAYBACK",
    "guild_id": "string",
    "user_id": "string",
    "query": "string"
  }

## General Rules
- **Scope Restriction:** 모든 변경 사항은 해당 Submodule 범위 내로 엄격히 제한
- **Environment Variable Synchronization:** Docker Environment variables (`.env`)와 Cloudflare 라우팅 컨텍스트 동기화 상태 유지
- **Rule Delegation:** 상세 코딩 컨벤션, 개발 워크플로우, 브랜치 전략은 각 Submodule(`chichi`, `chichi-bot`) 내 `CLAUDE.md` 지침을 준수

## Output Style
- 서론/군더더기 없이 핵심 위주로 직관적이고 간결하게 답변
- 한국어로 항상 답변