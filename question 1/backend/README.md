# URL Shortener Microservice Backend

A production-grade HTTP URL Shortener microservice built with Node.js and Express, featuring:
- Custom logging middleware (logs sent via API, not console)
- JWT-based authentication for logging
- In-memory storage for URLs and stats
- Robust error handling

## Features
- **POST /shorturls**: Create a short URL (with optional custom shortcode and validity)
- **GET /:shortcode**: Redirect to the original URL
- **GET /shorturls/:shortcode**: Get usage stats for a shortcode
- **Custom Logging**: All major events are logged to an external service via API
- **Authentication**: JWT token is fetched and cached for logging

---

## Setup & Run

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Configure logger credentials:**
   - Edit `logger.js` and fill in your registration details in `REGISTRATION_DETAILS`.

3. **Start the server:**
   ```bash
   npm start
   ```
   The server runs on `http://localhost:3000` by default.

---

## API Endpoints

### 1. Create Short URL
- **POST /shorturls**
- **Body:**
  ```json
  {
    "url": "https://example.com",
    "shortcode": "custom123",   // optional, must be unique
    "validityMins": 60            // optional, default 30
  }
  ```
- **Response:**
  - `201 Created`:
    ```json
    { "shortcode": "abc1234", "expiresAt": 1710000000000 }
    ```
  - `400 Bad Request` for invalid URL or duplicate shortcode

### 2. Redirect to Original URL
- **GET /:shortcode**
- **Response:**
  - `302 Redirect` to original URL
  - `404 Not Found` if shortcode does not exist
  - `410 Gone` if shortcode expired

### 3. Get Short URL Stats
- **GET /shorturls/:shortcode**
- **Response:**
  - `200 OK`:
    ```json
    {
      "shortcode": "abc1234",
      "url": "https://example.com",
      "createdAt": 1710000000000,
      "expiresAt": 1710001800000,
      "usageCount": 5
    }
    ```
  - `404 Not Found` if shortcode does not exist

---

## Logging & Authentication
- All major events (success, error, warn) are logged via POST to `http://20.244.56.144/evaluation-service/logs`.
- The logger module handles registration and JWT token management automatically.
- No console logs are used for main logging.

## Error Handling
- All errors return JSON responses with appropriate status codes:
  - `400` for bad requests
  - `404` for not found
  - `410` for expired shortcodes
  - `500` for internal errors

---

## Notes
- This service uses in-memory storage. Restarting the server will clear all data.
- For production, consider using a persistent database (e.g., SQLite, MongoDB). 
