---
name: Manage services-amagi-tv customers and keys
description: Create a customer, set and retrieve keys, list keys and their versions on the services-amagi-tv (Callisto) API.
api: openapi/amagi-callisto-openapi.yml
operations: [createNewCustomerEntry, deleteExistingCustomer, setKey, getKey, headKey, listKeys, listVersions]
---

# Manage services-amagi-tv customers and keys

The services-amagi-tv (Callisto) API manages customers and their keys. Every request
requires **two** apiKey headers together: `access_key` and `secret_key`. Base host:
`https://services.amagi.tv`.

## Steps

1. **Create the customer** — `POST /create-customer` (`createNewCustomerEntry`). Returns
   `200`; a duplicate customer returns `409`.
2. **Set a key** — `POST /set-key` (`setKey`) to write a key value for the customer.
3. **Read keys** —
   - `GET /get-key` (`getKey`) fetches a key value.
   - `GET /head-key` (`headKey`) checks a key exists without returning its value.
   - `GET /list-keys` (`listKeys`) lists the customer's keys.
   - `GET /list-versions` (`listVersions`) lists versions of a key.
4. **Delete the customer** — `POST /delete-customer` (`deleteExistingCustomer`) when
   decommissioning.

## Rules

- Auth: send both `access_key` and `secret_key` headers on every call; missing/invalid
  credentials return `401`.
- Errors use the envelope `{"error":"<description>"}`
  (see `errors/amagi-problem-types.yml`).
- No pagination or idempotency-key surface is documented
  (see `conventions/amagi-conventions.yml`).
- Prefer `headKey` over `getKey` when you only need existence — it avoids returning the
  secret value.
