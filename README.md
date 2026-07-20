# zoho-oauth
Zoho oauth refresh and acces token generator

# Zoho OAuth 2.0 Authentication using Deluge

A complete example of implementing **Zoho OAuth 2.0 Authorization Code Flow** using **Deluge** for accessing the **Zoho Books API**.

This repository demonstrates how to:

- Create a Server-based OAuth Client
- Generate an Authorization Code
- Generate Refresh Token and Access Token
- Refresh the Access Token automatically
- Call Zoho Books REST APIs using Deluge

---

# OAuth 2.0 Workflow

```text
                 ┌──────────────────────────────┐
                 │ Create Server-based Client   │
                 └──────────────┬───────────────┘
                                │
                                ▼
                   Client ID + Client Secret
                                │
                                ▼
                   Generate Authorization Code
                       (One-Time Process)
                                │
                                ▼
          Exchange Authorization Code for Tokens
                                │
                                ▼
           Access Token + Refresh Token Generated
                                │
                                ▼
             Call Zoho Books APIs using
                 Authorization Header
                                │
                                ▼
              Access Token Expires (~1 Hour)
                                │
                                ▼
             Use Refresh Token to Generate
                  New Access Token
                                │
                                ▼
                  Continue API Requests
```

---

# Project Structure

```
.
│
├── OAuth_Process.deluge
├── README.md
```

---

# Prerequisites

- Zoho Account
- Zoho Books Organization
- Deluge Environment
- Server-based OAuth Client
- Client ID
- Client Secret

---

# Step 1 - Create OAuth Client

Navigate to

https://api-console.zoho.com

Create

```
Server-based Application
```

Provide

- Client Name
- Homepage URL
- Redirect URI

Example

```
Homepage URL
https://example.com

Redirect URI
https://example.com/oauth/callback
```

After creation, Zoho provides

```
Client ID

Client Secret
```

---

# Step 2 - Generate Authorization Code

Open

```
https://accounts.zoho.com/oauth/v2/auth
```

Parameters

| Parameter | Value |
|-----------|-------|
| response_type | code |
| client_id | Your Client ID |
| redirect_uri | Redirect URL |
| scope | ZohoBooks.fullaccess.all |
| access_type | offline |
| prompt | consent |

Example

```
https://accounts.zoho.com/oauth/v2/auth?
response_type=code
&client_id=YOUR_CLIENT_ID
&scope=ZohoBooks.fullaccess.all
&redirect_uri=https://example.com
&access_type=offline
&prompt=consent
```

After login

```
https://example.com/?code=xxxxxxxxxxxx
```

Copy the Authorization Code.

---

# Step 3 - Generate Refresh Token

POST

```
https://accounts.zoho.com/oauth/v2/token
```

Parameters

| Parameter |
|-----------|
| grant_type=authorization_code |
| client_id |
| client_secret |
| redirect_uri |
| code |

Response

```json
{
    "access_token":"xxxxxxxx",
    "refresh_token":"xxxxxxxx",
    "expires_in":3600,
    "api_domain":"https://www.zohoapis.com"
}
```

> **Important**
>
> Save the Refresh Token securely.
> It is returned only once.

---

# Step 4 - Generate Access Token

Whenever the Access Token expires

POST

```
https://accounts.zoho.com/oauth/v2/token
```

Parameters

```
grant_type=refresh_token
refresh_token=YOUR_REFRESH_TOKEN
client_id=YOUR_CLIENT_ID
client_secret=YOUR_CLIENT_SECRET
```

Response

```json
{
    "access_token":"xxxxxxxx",
    "expires_in":3600
}
```

---

# Step 5 - Call Zoho Books API

Header

```
Authorization:
Zoho-oauthtoken ACCESS_TOKEN
```

Example

```
GET
https://www.zohoapis.com/books/v3/contacts
```

Example Deluge

```java
headers = Map();
headers.put("Authorization","Zoho-oauthtoken " + access_token);

response = invokeurl
[
    url :"https://www.zohoapis.com/books/v3/contacts?organization_id=YOUR_ORG_ID"
    type :GET
    headers:headers
];

info response;
```

---

# Access Token Lifecycle

| Token | Validity | Usage |
|--------|----------|-------|
| Client ID | Permanent | Application Identification |
| Client Secret | Permanent | Application Authentication |
| Authorization Code | One-Time | Generate Tokens |
| Refresh Token | Long-lived | Generate Access Token |
| Access Token | ~1 Hour | Call APIs |

---

# Deluge Workflow

```
Generate Access Token
        │
        ▼
Store Access Token
        │
        ▼
Create Authorization Header
        │
        ▼
Call Zoho Books API
        │
        ▼
Receive JSON Response
```

---

# Security Best Practices

- Never commit Client Secret.
- Never expose Refresh Token.
- Store credentials securely.
- Use environment variables whenever possible.
- Regenerate credentials if they are compromised.

---

# Technologies Used

- Deluge
- Zoho OAuth 2.0
- Zoho Books REST API
- invokeUrl

---

# API Used

```
GET /books/v3/contacts
```

Documentation

https://www.zoho.com/books/api/v3/

---

# Author

**Mohan M**

Zoho Developer

Specialized in

- Zoho Creator
- Zoho CRM
- Zoho Books
- Deluge
- API Integration
- Marketplace Extensions

---

If you found this project useful, consider giving it a ⭐ on GitHub.
