# Xero PKCE OAuth 2.0 with Postman

A Postman collection for authenticating to the Xero API using a PKCE (Proof Key for Code Exchange) app.

## Prerequisites

- Postman desktop app installed (browser extension does not support environment variables)
- A PKCE app created at [developer.xero.com/myapps](https://developer.xero.com/myapps) with your `client_id` and redirect URI ready

---

## Phase 1: Setup

### 1. Import the collection and environment

Download `Xero OAuth 2.0.postman_collection.json` and `OAuth 2.0.postman_environment.json` from this repo and import them via **File → Import** in Postman.

### 2. Configure your environment variables

1. Select the **OAuth 2.0** environment from the environment dropdown (top right of Postman)
2. Click the eye icon → **Edit**
3. Fill in the following variables:

| Variable | Value |
|---|---|
| `client_id` | Your app's client ID from developer.xero.com |
| `re_directURI` | Your app's redirect URI |
| `scopes` | Space-separated list of scopes (see below) |

**Recommended scopes to start:**
```
openid profile email offline_access accounting.transactions accounting.contacts accounting.settings
```

> `offline_access` is required to receive a refresh token.

[SCREENSHOT NEEDED: Postman environment editor with client_id, re_directURI, and scopes filled in]

---

## Phase 2: Get a Token

### 3. Configure OAuth 2.0 with PKCE in Postman

1. Double-click the **Get started** request in the collection
2. Select the **Authorization** tab
3. Set **Type** to `OAuth 2.0`
4. Under **Configure New Token**, fill in:

| Field | Value |
|---|---|
| Token Name | `Xero` |
| Grant Type | `Authorization Code (With PKCE)` |
| Callback URL | `{{re_directURI}}` |
| Auth URL | `https://login.xero.com/identity/connect/authorize` |
| Access Token URL | `https://identity.xero.com/connect/token` |
| Client ID | `{{client_id}}` |
| Code Challenge Method | `SHA-256` |
| Scope | `{{scopes}}` |
| State | `{{state}}` |

[SCREENSHOT NEEDED: Postman Authorization tab showing PKCE configuration filled in]

5. Click **Get New Access Token**
6. Log in to Xero in the browser window that opens
7. Select the organisation to connect and grant access

### 4. Set your access and refresh tokens

Once authenticated, Postman shows the **Manage Access Tokens** dialog:

1. Highlight the **Access Token** value → right-click → **Set: OAuth 2.0 → access_token**
2. Scroll down → highlight the **Refresh Token** value → right-click → **Set: OAuth 2.0 → refresh_token**

[SCREENSHOT NEEDED: Manage Access Tokens dialog with Access Token highlighted, right-click menu visible]

### 5. Get your tenant ID

1. Double-click the **Connections** request
2. Click **Send**

The test script automatically sets `xero-tenant-id` from the first organisation in the response.

[SCREENSHOT NEEDED: Connections response body showing tenantId field in the JSON array]

---

## Phase 3: Make API Calls

### 6. Make your first API call

1. Double-click the **Invoices** request
2. Click **Send**

The request automatically refreshes your access token before sending.

### 7. Refresh the token manually

Your access token expires after 30 minutes. To refresh manually:

1. Double-click the **Refresh token** request
2. Click **Send**

The test script automatically updates `access_token` and `refresh_token` in your environment.

---

## Notes

- To connect to a different organisation, repeat steps 3–5 and select a different organisation.
- Available scopes: [developer.xero.com/documentation/oauth2/scopes](https://developer.xero.com/documentation/oauth2/scopes)
