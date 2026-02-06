# Tymoe Traefik Gateway

This repository contains the Traefik API Gateway configuration for the Tymoe microservices architecture. It handles routing, SSL termination (future), and centralized authentication through ForwardAuth.

## Architecture

- **Gateway**: Traefik v3.0
- **Routing**: Path-based and Host-based routing.
- **Security**: Integrates with `auth-service` via `ForwardAuth` middleware to validate JWT tokens before requests reach protected services.

## Directory Structure

- `docker-compose.yml`: Production deployment configuration.
- `docker-compose.test.yml`: Local testing configuration for development.
- `config/dynamic_conf.yml`: Dynamic routing and middleware definitions.
- `config/dynamic_conf.test.yml`: Mock configuration for testing.

## Getting Started

### Prerequisites

- Docker and Docker Compose
- A running instance of `auth-service` (for authentication)

### Deployment

To start the gateway in production mode:

```bash
docker compose up -d
```

### Local Testing

If you are testing on a local machine or M910Q:

```bash
docker compose -f docker-compose.test.yml up -d
```

## Configuration

The gateway is configured to route traffic as follows:

- `/api/auth-service/*` -> `auth-service:8080`
- `/api/subscription-service/*` -> `subscription-service:8088`

Protected routes require a valid Bearer Token, which is validated by calling the `auth-service` gateway-check endpoint.

## License

MIT - Tymoe Technologies
