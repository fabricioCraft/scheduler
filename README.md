# Scheduler API

A webhook scheduling service built by **DinastIA Community** - the largest AI Agents community in Brazil.

## Overview

This API allows you to schedule webhook calls for specific timestamps. Messages are stored in Redis and executed only once at the specified time.

### How It Works

1. **Message Scheduling**: When you create a scheduled message, it's stored in Redis and added to the internal scheduler
2. **One-time Execution**: Jobs are scheduled to run only once at the specified timestamp
3. **Persistence**: On server restart, all messages from Redis are automatically restored and rescheduled
4. **Cleanup**: After webhook execution, messages are automatically removed from Redis

## Prerequisites

- Python 3.x
- Redis server running locally
- Required dependencies (install with `pip install -r requirements.txt`)

## Environment Setup

Create a `.env` file with:

```env
# Redis Configuration
REDIS_HOST=localhost
REDIS_PORT=6379

# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
API_TOKEN=your-secret-token-here
```

## Running the Server

```bash
python scheduler_api.py
```

The server will start on `http://localhost:8000`

## Authentication

All endpoints (except `/health`) require Bearer token authentication:

```
Authorization: Bearer your-secret-token-here
```

## API Endpoints

### Schedule a Message

`POST /messages`

**Headers:**
- `Authorization: Bearer your-secret-token-here`
- `Content-Type: application/json`

**Body:**
```json
{
  "id": "unique-message-id",
  "scheduleTo": "2024-12-25T10:30:00Z",
  "payload": {
    "data": "your webhook payload"
  },
  "webhookUrl": "https://your-webhook-endpoint.com"
}
```

**Response:**
```json
{
  "status": "scheduled",
  "messageId": "unique-message-id"
}
```

### List Scheduled Messages

`GET /messages`

**Headers:**
- `Authorization: Bearer your-secret-token-here`

**Response:**
```json
{
  "scheduledJobs": [
    {
      "messageId": "unique-message-id",
      "nextRun": "2024-12-25T10:30:00",
      "job": "<function job at 0x...>"
    }
  ],
  "count": 1
}
```

### Delete a Scheduled Message

`DELETE /messages/{message_id}`

**Headers:**
- `Authorization: Bearer your-secret-token-here`

**Response:**
```json
{
  "status": "deleted",
  "messageId": "unique-message-id"
}
```

### Health Check

`GET /health`

No authentication required.

**Response:**
```json
{
  "status": "healthy",
  "redis": "connected"
}
```

## Error Codes

- `401` - Missing or invalid authentication token
- `404` - Message not found (when deleting)
- `409` - Message with ID already exists (when creating)
- `500` - Internal server error

## About DinastIA Community

This project is developed by **DinastIA Community**, the largest AI Agents community in Brazil, dedicated to advancing artificial intelligence and automation technologies.