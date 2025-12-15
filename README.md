# AdityaCore Major API Documentation

**Content-Type:** `application/json`

---

## 🔐 Authentication
All endpoints (except Register and Login) require a JSON Web Token (JWT) in the request header.

**Header Format:**

```http
Authorization: Bearer <your_jwt_token>

```

---

## 1. User Authentication
### Register UserCreates a new user account. By default, the status is set to `PENDING_APPROVAL`.

* **Endpoint:** `/MajorRegister`
* **Method:** `POST`
* **Auth Required:** No

**Request Body:**

```json
{
  "username": "User123",  // Alphanumeric, 1-15 characters
  "password": "securePassword"
}

```

**Responses:**

* **Success (200 OK):**
```json
{ "OK": true }

```


* **Error (Validation/Exists):**
```json
{ "error": "VALIDATION_ERROR" }

```



---

### Login UserAuthenticates a user. A JWT is only returned if the user status is `ACTIVE`.

* **Endpoint:** `/MajorLogin`
* **Method:** `POST`
* **Auth Required:** No

**Request Body:**

```json
{
  "username": "User123",
  "password": "securePassword"
}

```

**Responses:**

* **Success (Active User):**
```json
{
  "username": "User123",
  "jwt": "eyJhbGciOiJIUzI1NiIsInR...",
  "status": "ACTIVE"
}

```


* **Success (Pending/Suspended User):**
*Token is NOT provided.*
```json
{ "status": "PENDING_APPROVAL" }

```


* **Error (Wrong Password/User Not Found):**
```json
{ "error": "INCORRECT_CREDENTIALS" }

```



---

## 2. Core Data
### List Available AccountsFetches the complete list of FreeFire accounts available in the system.

* **Endpoint:** `/MajorList`
* **Method:** `GET`
* **Auth Required:** Yes

**Responses:**

* **Success:**
```json
[
  {
    "account_id": "123456789",
    "nickname": "ProPlayerOne",
    "status": "Taken" // Optional field, usually null if available
  },
  ...
]

```


* **Error (Server/Auth):**
```json
{ "error": "VALIDATION_ERROR" } // or "ACCESS_DENIED"

```



---

## 3. Request Management
### Create RequestSubmit a request for a specific account. Fails if the account is taken or if the user already has an active request.

* **Endpoint:** `/MajorRequestCreate`
* **Method:** `POST`
* **Auth Required:** Yes

**Request Body:**

```json
{
  "account_id": "123456789"
}

```

**Responses:**

* **Success:**
```json
{ "OK": true }

```


* **Error (Account not found/Taken/Duplicate Request):**
```json
{ "error": "ACCESS_DENIED" }

```



---

### List User RequestsFetches all requests made by the logged-in user. If a request is `APPROVED`, credential details (`uid`, `password`) are included.

* **Endpoint:** `/MajorRequestsList`
* **Method:** `GET`
* **Auth Required:** Yes

**Responses:**

* **Success:**
```json
[
  {
    "account_id": "123456789",
    "status": "CREATED",
    "created_at": "2023-10-27T10:00:00.000Z"
  },
  {
    "account_id": "987654321",
    "status": "APPROVED",
    "created_at": "2023-10-26T10:00:00.000Z",
    "uid": "100200300",       // Only visible if APPROVED
    "password": "accountPass" // Only visible if APPROVED
  }
]

```



---

### Cancel RequestCancels a pending request.

* **Endpoint:** `/MajorRequestCancel`
* **Method:** `POST`
* **Auth Required:** Yes

**Request Body:**

```json
{
  "account_id": "123456789"
}

```

**Responses:**

* **Success:**
```json
{ "OK": true }

```


* **Error (Not found/Already Cancelled):**
```json
{ "error": "VALIDATION_ERROR" }

```



---

## 4. Gift System
### List GiftsFetches accounts gifted specifically to the user. If the user has claimed the gift, credentials are revealed.

* **Endpoint:** `/MajorGiftsList`
* **Method:** `GET`
* **Auth Required:** Yes

**Responses:**

* **Success:**
```json
[
  {
    "account_id": "555666777",
    "status": "UNCLAIMED"
  },
  {
    "account_id": "111222333",
    "status": "CLAIMED",
    "uid": "111222333",       // Only visible if CLAIMED
    "password": "giftPassword" // Only visible if CLAIMED
  }
]

```



---

### Claim GiftMarks a gift as claimed, allowing the user to view the credentials via the List endpoint.

* **Endpoint:** `/MajorGiftClaim`
* **Method:** `POST`
* **Auth Required:** Yes

**Request Body:**

```json
{
  "account_id": "555666777"
}

```

**Responses:**

* **Success:**
```json
{ "OK": true }

```


* **Error (Gift not found/Already Claimed):**
```json
{ "error": "ACCESS_DENIED" }

```
