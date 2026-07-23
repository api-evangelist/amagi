---
name: Provision and manage Mapsor users
description: Create a Mapsor user, (re)generate their access token, and enable or disable the account, then submit a container job on their behalf.
api: openapi/amagi-mapsor-openapi.yml
operations: [createNewUserEntry, regenerateTokenForExistingUser, enableUser, disableUser, createContainerJob]
---

# Provision and manage Mapsor users

Mapsor is Amagi's job-orchestration + user/token service. All calls authenticate with an
apiKey passed as the `token` query parameter (scheme `TokenAuth`). Base host:
`https://api.mapsor.amagi.tv`.

## Steps

1. **Create the user** — `POST /add-user` (`createNewUserEntry`). Returns `201` with the
   new user's generated token. On a duplicate you get `409`.
2. **Rotate the token when needed** — `POST /regenerate-token`
   (`regenerateTokenForExistingUser`). Use this on suspected compromise; the old token stops
   working immediately.
3. **Enable / disable access** — `POST /enable-user` (`enableUser`) and `POST /disable-user`
   (`disableUser`) toggle the account without deleting it.
4. **Submit work** — `POST /submit` (`createContainerJob`) with the job object (`command`,
   `dockerImage`, `region`, `queue`, `priority`, `timeout`, `tags`, `env`). Returns `200`
   with a `jobID`.

## Rules

- Auth: send `?token=<api-key>` on every request. A missing/invalid token returns `401`;
  a valid token lacking permission returns `403`.
- No idempotency key is supported (see `conventions/amagi-conventions.yml`) — do not blindly
  retry `POST /submit` or `POST /add-user`; check for `409`/existing state first.
- Errors use the envelope `{"error":{"status","message","code"}}`
  (see `errors/amagi-problem-types.yml`).
- Job status/logs/cancel/retry endpoints exist but are unnamed in the spec
  (`GET /status/{id}`, `/logs/{id}`, `/cancel/{id}`, `/retry/{id}`); poll `GET /status/{id}`
  after submit.
