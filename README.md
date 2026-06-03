# JWT Authentication API

A simple JWT authentication system built with Node.js and Express featuring access tokens and refresh tokens across two separate servers.

## Project Structure


├── authServer.js
├── server.js
├── package.json
└── .env
```

## Servers

There are two servers in this project. authServer.js runs on port 4000 and handles all authentication. server.js runs on port 3000 and handles protected routes.

## Getting Started

Make sure you have Node.js and npm installed. Then clone the repository and run:

```bash
npm install
```

Create a `.env` file in the root directory:

```
ACCESS_TOKEN_SECRET=your_access_token_secret
REFRESH_TOKEN_SECRET=your_refresh_token_secret
```

Generate secrets by running this in your terminal:

```bash
node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"
```

## Running the Servers

Run both servers in separate terminals:

```bash
npm run devstart        # server.js on port 3000
npm run devstartAuth    # authServer.js on port 4000
```

## API Endpoints

### Auth Server (port 4000)

Login with a username to get both tokens:
```
POST /login
Body: { "username": "Jim" }
```

Get a new access token when the old one expires:
```
POST /token
Body: { "token": "<refreshToken>" }
```

Logout and invalidate the refresh token:
```
DELETE /logout
Body: { "token": "<refreshToken>" }
```

### Main Server (port 3000)

Get posts for the logged in user:
```
GET /posts
Header: Authorization: Bearer <accessToken>
```

## How it Works

Login first by calling POST /login on port 4000 with a username. You will get back an access token and a refresh token. Use the access token to call GET /posts on port 3000. The access token expires in 30 seconds. When it expires use POST /token with your refresh token to get a new access token. When you are done call DELETE /logout to invalidate your refresh token.

## Token Details

Access token expires in 30 seconds and is signed with ACCESS_TOKEN_SECRET. Refresh token never expires and is signed with REFRESH_TOKEN_SECRET. Refresh tokens are stored in memory so they reset every time the server restarts. Always call POST /login again after a restart.

## Status Codes

401 means no token was provided. 403 means the token is invalid, expired or not in the refresh token list. 204 means logout was successful with no content returned.

## Dependencies

- express
- jsonwebtoken
- dotenv
- nodemon (dev)

