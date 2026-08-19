---
name: Create a patient and submit a fill request (FuzeRx)
description: Register a patient with FuzeRx, find their prescription, check NDC availability, and submit a fill request — then reconcile the asynchronous ORDER outcome.
api: openapi/truepill-patients-api-openapi.yml, openapi/truepill-fulfillment-api-openapi.yml, openapi/truepill-webhooks-api-openapi.yml
generated: '2026-08-15'
method: generated
source: openapi/truepill-*-openapi.yml + https://rxdocs.fuzehealth.com
operations:
  - putV1Patient
  - getV1PatientPatient_tokenPrescriptions
  - getV1Availability
  - postV1Fill_request
  - getV1Fill_requestUrl_token
  - getV1Fill_requestUrl_tokenWebhook_events
  - postV1Cancel_request
---

# Create a patient and submit a fill request

Base URL `https://rxapi.fuzehealth.com` (sandbox: `https://rxapi.sandbox.fuzehealth.com`).
Every call carries `Authorization: ApiKey <YOUR API KEY>`.

## Before you start

- **This flow dispenses medication to a real person.** Treat every write step as safety-critical.
- **There is no idempotency key on this API.** If a POST times out, do NOT resend it. Recover by
  reading, as described in step 6.
- Work in sandbox first. Sandbox keys are prefixed `tp_test_key_`, production keys `tp_live_key_`.
  A sandbox token returns 404 against production and vice versa.

## Steps

### 1. Create the patient — with PUT, not POST

`putV1Patient` — `PUT /v1/patient`

This is the single most common mistake on this API: **PUT creates, POST updates.** Required fields
are `first_name`, `last_name`, `gender`, `dob`. Supply the address and phone exactly as they were
given to the prescriber on the e-Script; those attributes are used for record matching.

Returns `201` with `{ "patient_token": "..." }`. If the demographics match an existing record, the
existing `patient_token` comes back instead of a new one — this call is naturally
match-on-create, which is the closest thing to safe replay this API offers.

On `400`, read `validation_errors[]` and fix the named `key`. An empty string is never accepted for
any field, required or optional — omit the field instead of sending `""`.

### 2. Find the prescription

`getV1PatientPatient_tokenPrescriptions` — `GET /v1/patient/{patient_token}/prescriptions`

Prescriptions are **not created through this API**. They arrive at the FuzeRx pharmacy over
Surescripts as an eScript, or via a transfer. Poll this endpoint, or wait for the `NOTIFY_RX`
webhook event, until the prescription appears.

Pick a prescription whose `fillable` is `true`. Keep its `prescription_token`.

### 3. Check availability before you commit

`getV1Availability` — `GET /v1/availability`

Check the prescribed NDC is dispensable before submitting. Doing this first turns an asynchronous
rejection into a synchronous answer.

### 4. Submit the fill request

`postV1Fill_request` — `POST /v1/fill_request`

Reference the `patient_token` and the prescription(s). Set a `metadata` string of your own — it is
echoed on every downstream webhook event and is the practical join key for reconciliation.

**A 200/202 means accepted, not filled.** The response carries a `url_token` for the fill request
and a `request_id` of the form `fill_request_<hex>`.

### 5. Wait for the outcome

The real result arrives as an `ORDER` webhook event POSTed to your configured endpoint, carrying
`status` of `success`, `error` or `triage`, and `details.order_token` on success.

If you did not receive it, pull it:

`getV1Fill_requestUrl_tokenWebhook_events` — `GET /v1/fill_request/{url_token}/webhook_events`

or the account-wide feed, `getV1Webhook_eventsWebhook_type`.

### 6. If a POST timed out — read, do not retry

Call `getV1Fill_requestUrl_token` (`GET /v1/fill_request/{url_token}`) if you captured the token, or
pull the webhook-event feed and match on your `metadata` string.

If you retried anyway and see order error code **`R1` — "Duplicate Order"**, that is confirmation
your *first* attempt landed. The message names the original order number. Do not treat R1 as a
failure to fix; treat it as a successful first submission.

### 7. Cancelling

`postV1Cancel_request` — `POST /v1/cancel_request`. Cancellation is only possible while the order
has not yet been dispensed.

## Errors you will actually hit

| Status | Meaning | What to do |
|---|---|---|
| 400 | Validation | Read `validation_errors[]`, fix the named `key`, resubmit |
| 401 | Bad or missing key | `Authorization: ApiKey <key>`; check the key matches the host |
| 403 | Not entitled | Ask Fuze Health to enable the product on your account |
| 404 | Unknown token | Tokens are environment-scoped — a sandbox token 404s in production |
| 500 | Server error | Safe to retry GETs. **Never blind-retry the POST in step 4** |

Business rejections do not come back as HTTP errors — they arrive as webhook events with
`status: error` and a code. See `errors/truepill-error-codes.yml`.

## References

- `conventions/truepill-conventions.yml` — auth, pagination, tokens, the PUT/POST inversion
- `errors/truepill-problem-types.yml` — the HTTP error envelope
- `errors/truepill-error-codes.yml` — order rejection and triage codes
- `asyncapi/truepill-webhooks.yml` — the full event catalogue
- `sandbox/truepill-sandbox.yml` — how to simulate a prescription so this flow runs end to end
