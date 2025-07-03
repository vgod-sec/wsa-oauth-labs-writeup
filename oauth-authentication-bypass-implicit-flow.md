# Lab: Authentication Bypass via OAuth Implicit Flow

##  Lab Description
This lab demonstrates a vulnerability where flawed implementation of the OAuth implicit flow allows an attacker to log into other users' accounts without their credentials. The client application improperly trusts user-supplied information received from the OAuth server, leading to an authentication bypass.

**Target:** Log in as `carlos@carlos-montoya.net`  
**Credentials (for OAuth login):**  
- Username: `wiener`  
- Password: `peter`  


## Steps to Reproduce

### 1. **Intercept OAuth Flow**
- Launch Burp Suite and ensure it is intercepting traffic.
- Navigate to the lab and click **"My account"**.
- Log in using the OAuth credentials `wiener:peter`.
- You will be redirected back to the application after successful login.

### 2. **Analyze HTTP Traffic**
- In Burp, go to **Proxy > HTTP History**.
- Look for the initial `GET /auth?client_id=...` request which begins the OAuth flow.
- Continue examining until you find a `POST /authenticate` request made by the client application.

### 3. **Inspect the Authenticate Request**
- The `POST /authenticate` request contains:
  - OAuth `access_token`
  - User information including email
- This request is what the blog uses to log the user in.

### 4. **Modify the Email Parameter**
- Send the `POST /authenticate` request to **Burp Repeater**.
- In the Repeater, **change the `email` field** to:  
  `carlos@carlos-montoya.net`
- Send the modified request.

### 5. **Replay Session to Confirm Bypass**
- If no error occurs, right-click the same `POST` request and choose:
  - **Request in browser > In original session**
- Copy the generated URL and open it in the browser.
- You should now be logged in as **Carlos**, solving the lab.

##  Mitigation
- The client application **must not trust** data from the OAuth provider without proper validation.
- OAuth tokens should be exchanged securely (preferably via **Authorization Code flow**) and verified server-side.
- Ensure that **access tokens** are validated against the OAuth provider to retrieve and verify user identity before establishing a session.

##  Summary
> Insecure implementation of the OAuth implicit flow allows attackers to manipulate identity parameters and log in as other users.
