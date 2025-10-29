# reCAPTCHA Service REST API

A Node.js REST API service demonstrating Google reCAPTCHA v2 (Invisible) and v3 implementation with Express and server-side verification.

## Features

- ✅ **reCAPTCHA v2 (Invisible)** - Challenge-based verification with invisible captcha
- ✅ **reCAPTCHA v3** - Score-based bot detection without user interaction
- ✅ **RESTful API** - JSON-based endpoints for captcha verification
- ✅ **CORS Enabled** - Ready for cross-origin requests
- ✅ **Server-side Verification** - Secure token validation on the backend
- ✅ **Demo Pages** - Interactive examples for both reCAPTCHA versions

## Tech Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **View Engine**: Handlebars (HBS)
- **HTTP Client**: request-promise
- **Environment Config**: dotenv

## Prerequisites

- Node.js (v10 or higher recommended)
- npm or yarn
- Google reCAPTCHA credentials (both v2 and v3)

## Getting Started

### 1. Get reCAPTCHA Credentials

Visit the [Google reCAPTCHA Admin Console](https://www.google.com/recaptcha/admin) to register your site and obtain:
- v2 Site Key & Secret Key
- v3 Site Key & Secret Key

### 2. Configure Environment Variables

Create a `.env` file in the project root:

```env
V2_PUBLIC=your_v2_site_key
V2_PRIVATE=your_v2_secret_key
V3_PUBLIC=your_v3_site_key
V3_PRIVATE=your_v3_secret_key
PORT=3000
```

### 3. Install Dependencies

```bash
npm install
```

### 4. Run the Application

**Production mode:**
```bash
npm start
```

**Development mode (with auto-reload):**
```bash
npm run start:dev
```

The server will start at `http://localhost:3000`

## API Endpoints

### Web Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Home page with links to demo pages |
| GET | `/v2` | reCAPTCHA v2 demo page |
| GET | `/v3` | reCAPTCHA v3 demo page |

### API Routes

#### Verify reCAPTCHA v2

```http
POST /v2
Content-Type: application/json

{
  "g-recaptcha-response": "<token_from_client>"
}
```

**Response:**
```json
{
  "success": true,
  "challenge_ts": "2024-01-01T00:00:00Z",
  "hostname": "localhost"
}
```

#### Verify reCAPTCHA v3

```http
POST /v3
Content-Type: application/json

{
  "token": "<token_from_client>"
}
```

**Response:**
```json
{
  "success": true,
  "score": 0.9,
  "action": "v3",
  "challenge_ts": "2024-01-01T00:00:00Z",
  "hostname": "localhost"
}
```

## Usage Example

### Client-side (v3 with Fetch API)

```javascript
// Get token from reCAPTCHA
const token = await grecaptcha.execute('YOUR_SITE_KEY', {action: 'submit'});

// Send to your backend for verification
const response = await fetch('/v3', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ token })
});

const result = await response.json();
if (result.success && result.score > 0.5) {
  // Proceed with form submission
}
```

## Important Notes

### Request Library Response Type
The `request` library returns responses as **strings**, not objects. Always parse the response:

```javascript
const result = JSON.parse(await request.post(url).form(data));
```

### FormData and AJAX
- `FormData` objects cannot be sent directly in fetch requests as they lack a `toString()` method
- Convert FormData to a plain object, then stringify it:

```javascript
const data = {};
for (const [key, val] of new FormData(form).entries()) {
  data[key] = val;
}
const body = JSON.stringify(data);
```

### reCAPTCHA v3 Scores
- Scores range from 0.0 (likely bot) to 1.0 (likely human)
- Recommended threshold: 0.5
- Implement fallback to v2 for low scores

## Project Structure

```
captcha-service-rest/
├── app.js              # Main Express application
├── views/              # Handlebars templates
│   ├── index.hbs      # Home page
│   ├── v2.hbs         # reCAPTCHA v2 demo
│   └── v3.hbs         # reCAPTCHA v3 demo
├── .env               # Environment variables (not in git)
├── .gitignore         # Git ignore rules
├── package.json       # Dependencies and scripts
└── README.md          # This file
```

## Security Considerations

- ⚠️ Never expose your **secret keys** in client-side code
- ⚠️ Always verify reCAPTCHA tokens on the server side
- ⚠️ Keep `.env` out of version control (already in `.gitignore`)
- ⚠️ Use environment variables for production deployments
- ⚠️ Implement rate limiting on your endpoints

## Troubleshooting

**Issue: "Invalid site key"**
- Verify your site key matches the domain in reCAPTCHA admin console
- Check that you're using the correct key for v2 vs v3

**Issue: "Verification failed"**
- Ensure you're using the correct secret key on the backend
- Check that the token hasn't expired (tokens are valid for 2 minutes)

**Issue: CORS errors**
- CORS is enabled by default in this service
- Update CORS configuration in `app.js` if needed

## Resources

- [Google reCAPTCHA Documentation](https://developers.google.com/recaptcha/docs/)
- [reCAPTCHA v3 Guide](https://developers.google.com/recaptcha/docs/v3)
- [reCAPTCHA v2 Invisible Guide](https://developers.google.com/recaptcha/docs/invisible)

## License

ISC

## Future Improvements

- [ ] Add hybrid v2+v3 login flow (v3 score triggers v2 challenge)
- [ ] Implement request logging and monitoring
- [ ] Add TypeScript support
- [ ] Create Docker containerization
- [ ] Add comprehensive test suite
- [ ] Implement rate limiting middleware
