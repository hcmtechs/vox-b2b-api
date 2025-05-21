# B2B API Documentation

This document describes the endpoints available for B2B clients to interact with the audio transcription service.

## Authentication

All API requests must include an API key in the `X-API-Key` header:

```http
X-API-Key: your_api_key_here
```

## Endpoints

### Create a Job

Creates a new transcription job for processing an audio/video file.

```http
POST /b2b/jobs
```

#### Request Body

```json
{
    "google_drive_url": "https://drive.google.com/file/d/...",  // Optional: Google Drive URL
    "media_url": "https://example.com/audio.mp3",              // Optional: Direct media URL
    "language": "tr"                                           // Optional: ISO language code (e.g., 'tr' for Turkish)
}
```

**Note:** You must provide either `google_drive_url` or `media_url`, but not both.

#### Response

```json
{
    "id": 123,
    "client_id": 456,
    "google_drive_url": "https://drive.google.com/file/d/...",
    "media_url": null,
    "file_url": null,
    "language": "tr",
    "duration_seconds": null,
    "status": "PENDING",
    "created_at": "2024-03-20T10:00:00Z",
    "updated_at": "2024-03-20T10:00:00Z",
    "task_id": null,
    "error_message": null
}
```

### List Jobs

Retrieves a list of jobs for the authenticated client.

```http
GET /b2b/jobs?skip=0&limit=100
```

#### Query Parameters

- `skip` (optional): Number of jobs to skip (default: 0)
- `limit` (optional): Maximum number of jobs to return (default: 100, max: 100)

#### Response

```json
{
    "data": [
        {
            "id": 123,
            "client_id": 456,
            "google_drive_url": "https://drive.google.com/file/d/...",
            "media_url": null,
            "file_url": null,
            "language": "tr",
            "duration_seconds": 120.5,
            "status": "FINISHED",
            "created_at": "2024-03-20T10:00:00Z",
            "updated_at": "2024-03-20T10:05:00Z",
            "task_id": "task-123",
            "error_message": null
        }
    ],
    "count": 1
}
```

### Get Job Details

Retrieves detailed information about a specific job.

```http
GET /b2b/jobs/{job_id}
```

#### Response

```json
{
    "id": 123,
    "client_id": 456,
    "google_drive_url": "https://drive.google.com/file/d/...",
    "media_url": null,
    "file_url": "https://storage.example.com/audio.ogg",
    "transcription_text": "Speaker 1: Hello everyone...",
    "notes_text": "- Meeting started...",
    "summary_text": "Meeting introduction",
    "title_text": "Team Meeting",
    "language": "tr",
    "duration_seconds": 120.5,
    "formatted_transcript": "**John Smith**: Hello everyone...",
    "status": "FINISHED",
    "created_at": "2024-03-20T10:00:00Z",
    "updated_at": "2024-03-20T10:05:00Z",
    "task_id": "task-123",
    "error_message": null
}
```

## Job Status

Jobs can have the following statuses:

- `PENDING`: Job has been created and is waiting to be processed
- `IN_PROGRESS`: Job is currently being processed
- `FINISHED`: Job has been successfully completed
- `ERROR`: Job failed to process

## Webhook Notifications

If a webhook URL is configured for your client, you will receive POST requests with job status updates. The webhook URL can be configured by an administrator.

### Successful Job Webhook Payload

```json
{
    "job_id": 123,
    "status": "FINISHED",
    "transcription_text": "Speaker 1: Hello everyone...",
    "formatted_transcript": "**John Smith**: Hello everyone...",
    "summary_text": "Meeting introduction",
    "notes_text": "- Meeting started...",
    "title_text": "Team Meeting",
    "created_at": "2024-03-20T10:00:00Z",
    "updated_at": "2024-03-20T10:05:00Z"
}
```

### Failed Job Webhook Payload

```json
{
    "job_id": 123,
    "status": "ERROR",
    "error_message": "Failed to download file",
    "created_at": "2024-03-20T10:00:00Z",
    "updated_at": "2024-03-20T10:01:00Z"
}
```

## Error Responses

The API uses standard HTTP status codes to indicate the success or failure of requests:

- `200 OK`: Request succeeded
- `400 Bad Request`: Invalid request parameters
- `401 Unauthorized`: Invalid or missing API key
- `403 Forbidden`: Inactive client
- `404 Not Found`: Resource not found
- `500 Internal Server Error`: Server error

Example error response:

```json
{
    "detail": "Invalid API key"
}
```

## Best Practices

1. Always check the job status before attempting to access job details
2. Implement webhook handling to receive real-time job status updates
3. Use appropriate error handling for failed requests
4. Keep your API key secure and do not share it
5. Monitor job statuses and handle errors appropriately
6. Use pagination when listing jobs to manage large datasets efficiently 
