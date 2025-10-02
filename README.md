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
* Payrails sandbox credentials (`api_key` and `client_id`).
* Node.js v18+ (optional, for encryption scripts).

---

## 3. Environment Variables

| Variable Name       | Description                                                                     |
| ------------------- | ------------------------------------------------------------------------------- |
| base_url            | The base URL for the Payrails API endpoints                                     |
| client_id           | The client identifier used for authentication                                   |
| api_key             | The API key for accessing the Payrails services                                 |
| access_token        | The token obtained from the authentication request, used to authorize API calls |
| public_key          | The public key used for encrypting card data                                    |
| holder_reference    | A reference identifier for the cardholder                                       |
| encrypted_card_data | The encrypted payment card data to be tokenized (JWE)                           |

---

## 4. Workflow and API Table

| Step | Endpoint                                 | Method | Request Body                                                                                                                                                              | Notes                                                                |
| ---- | ---------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| 1    | `{{base_url}}/auth/token/{{client_id}}`  | POST   | { "x-api-key": "{{api_key}}" }                                                                                                                                            | Request an access token and save it to the access_token environment variable.                           |
| 2    | `/public/payment/instruments/initialize` | POST   | { "type": "tokenization", "holderReference": "{{holder_reference}}" }                                                                                                     | Initialize client with Client Side Encryption. Save tokenization.publicKey as public_key for encryption.    |
| 3    | (Encryption Step)                        | N/A    | { "cardNumber": "4111111111111111", "expiryMonth": "03", "expiryYear": "30", "securityCode": "737", "holderName": "John Doe", "holderReference": "{{holder_reference}}" } | Action: Encrypt this JSON using JWE with public_key. Save the resulting value to encrypted_card_data.           |
| 4    | `/public/payment/instruments/tokenize`   | POST   | { "holderReference": "{{holder_reference}}", "encryptedInstrumentDetails": "{{encrypted_card_data}}", "futureUsage": "CardOnFile", "storeInstrument": true }              | Tokenize the encrypted card. The API returns 201 Created with the tokenized instrument ID.. |

---

## 5. Notes

* Ensure `holder_reference` in the body matches the one in the encrypted payload.
* The collection is ready to run against the Payrails mock server.
* You can modify variables to test different card details or references.

---

## 6. Submission

The exported Postman collection is included in this repository.
You can run it directly in Postman with environment variables configured.
