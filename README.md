# Payrails Product Assessment - Postman Collection

This repository contains the exported Postman collection for the **Payrails Product Assessment**.
It demonstrates the workflow to **request access tokens, initialize the client, encrypt payment card details, and tokenize cards** using the Payrails API.

---

## 1. Overview

The assessment covers:

1. Requesting an **access token** from Payrails.
2. Initializing the **client SDK** with Client Side Encryption (CSE).
3. Encrypting card details using the returned **public key** (JWE).
4. Creating a **Payment Instrument** via the tokenization endpoint.

All steps are implemented as requests inside this Postman collection, with variables for reusability.

---

## 2. Prerequisites

* [Postman](https://www.postman.com/) installed.
* Payrails sandbox credentials (`x-api-key`).
* Node.js v18+ (optional, for encryption scripts).

---

## 3. Environment Variables

| Variable Name       | Description                                     |
| ------------------- | ----------------------------------------------- |
| x_api_key           | Your Payrails API key                           |
| access_token        | Bearer token obtained from Request Access Token |
| holder_reference    | Reference for the customer/payment holder       |
| encrypted_card_data | JWE-encrypted payment details                   |
| base_url            | Payrails API base URL                           |

---

## 4. Steps in the Collection

### Step 1: Request Access Token

* Endpoint: `/public/auth/token`
* Method: POST
* Headers: `x-api-key`
* Save the returned `access_token` to environment variable.

### Step 2: Initialize Client

* Endpoint: `/public/payment/instruments/initialize`
* Method: POST
* Body:
  {
  "type": "tokenization",
  "holderReference": "{{holder_reference}}"
  }
* Save the returned `tokenization.publicKey` for encrypting card data.

### Step 3: Encrypt Card

* Use the public key returned in Step 2.
* Encrypt the card JSON using JWE with:

  * Algorithm: RSA-OAEP-256
  * Content Encryption: A256CBC-HS512

Example card JSON:
{
"cardNumber": "4111111111111111",
"expiryMonth": "03",
"expiryYear": "30",
"securityCode": "737",
"holderName": "John Doe",
"holderReference": "{{holder_reference}}"
}

* Save the resulting JWE to `encrypted_card_data`.

### Step 4: Tokenize Card

* Endpoint: `/public/payment/instruments/tokenize`

* Method: POST

* Body:
  {
  "holderReference": "{{holder_reference}}",
  "encryptedInstrumentDetails": "{{encrypted_card_data}}",
  "futureUsage": "CardOnFile",
  "storeInstrument": true
  }

* Returns `201 Created` with the ID of the tokenized instrument.

---

## 5. Notes

* Ensure `holderReference` in the body matches the one in the encrypted payload.
* The collection is ready to run against the Payrails mock server.
* You can modify variables to test different card details or references.

---

## 6. Submission

The exported Postman collection is included in this repository.
You can run it directly in Postman with environment variables configured.
