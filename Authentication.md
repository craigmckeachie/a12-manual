# Backend API

## API with Authentication

API (JSON Server) wrapped in Node.js code now requires authentication.

## Install

```bash
cd labs\solutions\project-manage
git checkout auth
npm install
```

> The npm install only adds the `jsonwebtoken` node package (library) which is used to create and parse JSON Web Tockens in the Node.js custom server

## Run

```bash
npm run api:auth
```

## Login API

```
POST http://localhost:3000/auth/login
```

### Request Content-Type

```
Content-Type: application/json
```

### Request Body

```
{
  "email": "craig@test.com",
  "password":"abc123"
}
```

- See `api\user-credentials.json` for additional valid email/password combinations or to change them.
  > Note: if you change data in `api\user-credentials.json` the server will need to be restarted before you will see the changes.

### Response Body (200 OK)

```
{
   "access_token": "XXXXXXXXXXXXXXXX"
}
```

## Authentication Required

Authentication is now required API for any json-server API calls including:

```
GET     http://localhost:3000/projects
GET     http://localhost:3000/projects/1
POST    http://localhost:3000/projects
PUT     http://localhost:3000/projects/1
DELETE  http://localhost:3000/projects/1
```

## Auth Request Header (required access token)

```
Authorization: Bearer XXXXXX
```

### Response Body (401 Unauthorized)

If access token is is expired or invalid (tampered with) the response will be:

```
{
  "status": 401,
  "message": "Error access token is invalid or has expired."
}
```

### Testing

To test run the requests in the files inside the `test` directory using a tool like [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) in Visual Studio Code.

- `login-post.http`
- `projects-get-auth.http`
  - The token that appears in the header after `Authorization: Bearer` will need to be updated with the `access_token` from the response received from `login-post.http`.
  - Leave the `Bearer` before supplying the token (XXXXX)
    `Authorization: Bearer XXXXXX`
    These are the same requests a client like an `Angular` application makes.

### Resources

[JSON Web Tokens (Basics/JWT)](https://medium.com/@piraveenaparalogarajah/json-web-tokens-jwt-basics-6515b13077e8)

[CORS Documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

[Uses OAuth 2](https://security.stackexchange.com/questions/108662/why-is-bearer-required-before-the-token-in-authorization-header-in-a-http-re)

[Preflight requests with CORS](http://restlet.com/company/blog/2015/12/15/understanding-and-using-cors/)

[Forked from](https://github.com/oz4you/mock-auth-json-server)

# Frontend Angular Application
