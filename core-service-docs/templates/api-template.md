# API Documentation Template

## Endpoint

**Method:** `GET | POST | PUT | DELETE`  
**Path:** `/api/v1/resource-name`

## Description

Short description of the API endpoint's purpose.

## Request

### Headers

```http
Authorization: Bearer <token>
Content-Type: application/json
Body (JSON)
json

{
  "field1": "value",
  "field2": 123,
  "field3": true
}
Response
Success (200 OK)
json

{
  "status": "success",
  "data": {
    "id": "abc123",
    "field1": "value",
    "created_at": "2025-01-01T00:00:00Z"
  }
}
Error (400/404/500)
json

{
  "status": "error",
  "message": "Invalid input",
  "code": 400
}

