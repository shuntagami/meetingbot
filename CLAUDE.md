# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

MeetingBot is an open-source API for sending bots to video meetings (Google Meet, Microsoft Teams, Zoom), recording them, and making the recordings available for applications. The infrastructure is defined with Terraform and deployed to AWS.

## Project Structure

The project is organized as a monorepo with these key components:

1. **Server** (`/src/server`): Next.js backend with tRPC APIs, authentication, and bot management
2. **Bots** (`/src/bots`): Platform-specific bot implementations for different meeting platforms:
   - Google Meet (`/src/bots/meet`)
   - Microsoft Teams (`/src/bots/teams`)
   - Zoom (`/src/bots/zoom`)
3. **Example App** (`/src/example-app`): Demo app showcasing MeetingBot integration
4. **Landing Page** (`/src/landing-page`): Project marketing site
5. **Terraform** (`/src/terraform`): Infrastructure as Code for AWS deployment

## Common Commands

### Root Project Commands

```bash
# Type checking across all projects
pnpm typecheck
```

### Server Commands

```bash
# Navigate to server directory
cd src/server

# Install dependencies
pnpm install

# Start development server
pnpm dev

# Build for production
pnpm build

# Start production server
pnpm start

# Run tests
pnpm test

# Run E2E tests
pnpm test:e2e

# Linting
pnpm lint
pnpm lint:fix

# Type checking
pnpm typecheck

# Database commands
pnpm db:generate  # Generate migrations
pnpm db:migrate   # Run migrations
pnpm db:push      # Push schema changes
pnpm db:studio    # Open Drizzle Studio
```

### Bot Commands

```bash
# Navigate to bots directory
cd src/bots

# Install dependencies
pnpm install

# Start bot locally
pnpm dev

# Run tests
pnpm test

# Build Docker images for bots
docker build -f meet/Dockerfile -t meet .
docker build -f teams/Dockerfile -t teams .
docker build -f zoom/Dockerfile -t zoom .

# Run a bot container
docker run --env-file .env <PLATFORM>  # Where <PLATFORM> is meet, teams, or zoom
```

### Example App Commands

```bash
# Navigate to example app directory
cd src/example-app

# Install dependencies
pnpm install

# Start development server
pnpm dev  # Runs on port 3002

# Build for production
pnpm build

# Start production server
pnpm start

# Linting
pnpm lint
```

### Terraform Commands

```bash
# Navigate to terraform directory
cd src/terraform

# Login to AWS
make sso  # Or: aws sso login --profile meetingbot

# Initialize Terraform
make init

# Select environment
terraform workspace select dev  # Or: terraform workspace new dev

# Plan changes
terraform plan

# Apply infrastructure
terraform apply

# Destroy infrastructure
terraform destroy
```

## Architecture

### Bot Flow

1. User or application requests a bot via server API
2. Server configures and deploys bot container via ECS
3. Bot joins meeting, authenticates, and sets identity
4. Bot records meeting and sends heartbeat updates to server
5. On completion, bot uploads recording to S3 and notifies server
6. Server provides signed URLs for accessing recordings

### Data Storage

- PostgreSQL database for bot configurations, API keys, and event tracking
- S3 for meeting recordings storage

### Authentication

- GitHub OAuth for admin dashboard access
- API key-based authentication for programmatic access

## Environment Setup

Each component requires specific environment variables. Example files are provided:

- Server: `/src/server/.env.example`
- Bots: `/src/bots/.env.example`
- Example App: `/src/example-app/.env.example`

For bot development, you can use the `envBotData.ts` script to generate the required `BOT_DATA` environment variable.

## Testing

The project uses Jest for unit and integration tests, and Playwright for end-to-end tests.

To run tests for a specific component:

```bash
# Server tests
cd src/server
pnpm test

# Bot tests
cd src/bots
pnpm test
```

## Deployment

The application is designed to be deployed on AWS using Terraform:

1. Configure AWS credentials and Terraform variables
2. Apply Terraform configuration to create infrastructure
3. Deploy Docker images for server and bots
4. Configure environment variables and DNS settings

See the Terraform README (`/src/terraform/README.md`) for detailed deployment instructions.