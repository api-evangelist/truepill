---
name: Determine a patient's copay and coverage (FuzeRx)
description: Attach an insurance record to a FuzeRx patient, run an eligibility check, and request a real-time copay determination — then read the asynchronous COPAY outcome including triage codes.
api: openapi/truepill-insurance-api-openapi.yml, openapi/truepill-webhooks-api-openapi.yml
generated: '2026-08-15'
method: generated
source: openapi/truepill-insurance-api-openapi.yml + https://rxdocs.fuzehealth.com
operations:
  - postV1Insurance
  - getV1InsuranceInsurance_token
  - postV1InsuranceEligibilitycheck
  - postV1Copay_request
  - getV1Copay_requestRequest_id
  - postV1Copay_requestCancel
  - postV1Coverage_request
  - postV1Prior_authorization
  - getV1Pharmacycoverage
  - getV1Webhook_eventsWebhook_type
---

# Determine a patient's copay and coverage

Base URL `https://rxapi.fuzehealth.com`. Header `Authorization: ApiKey <YOUR API KEY>`.

Copay determination is a **benefit adjudication against a real PBM**. It is asynchronous, it can
land in a `triage` state that needs a human, and the codes it returns are the ones that decide
whether a patient is quoted a price they will actually pay.

## Prerequisites

- A `patient_token` (see `truepill-create-patient-and-fill.md`).
- A `prescription_token` for a fillable prescription.
- The insurance-claim family is documented as **access-restricted** — a valid key can still get a
  `403` here until Fuze Health enables the entitlement on your account.

## Steps

### 1. Attach the insurance record

`postV1Insurance` — `POST /v1/insurance`

Supply the patient's pharmacy benefit details (BIN, PCN, group, cardholder id) against the
`patient_token`. Returns an `insurance_token`. Retrieve it later with
`getV1InsuranceInsurance_token` (`GET /v1/insurance/{insurance_token}`).

### 2. Check eligibility before quoting

`postV1InsuranceEligibilitycheck` — `POST /v1/insurance/eligibility-check`

Confirms the benefit is active before you spend a copay request on it. Cheaper to fail here than in
triage.

### 3. Request the copay

`postV1Copay_request` — `POST /v1/copay_request`

Reference the `patient_token`, `prescription_token` and `insurance_token`. Set your own `metadata`
string — it is echoed on the COPAY webhook event and is how you match the answer to the question.

Returns `202 Accepted` with a `request_id`. **That is not a price.** The determination arrives later.

### 4. Read the outcome

Either receive the `COPAY` webhook event, or poll:

- `getV1Copay_requestRequest_id` — `GET /v1/copay_request/{request_id}`
- `getV1Webhook_eventsWebhook_type` — `GET /v1/webhook_events/copay`, cursor-paginated, filterable
  with `timestamp=$gt:<epoch>`

`status` is one of:

- **`success`** — a determination was reached; read the out-of-pocket amount from `details`.
- **`error`** — the claim was rejected. Map the code via `errors/truepill-decline-codes.yml`.
- **`triage`** — a human at FuzeRx is working it. Codes `CT001`–`CT010`. Do **not** re-submit; a
  triage state is in progress, and a duplicate submission returns `DUPLICATE_REQUEST`.

### 5. Cancel if the patient walks away

`postV1Copay_requestCancel` — `POST /v1/copay_request/cancel`

### Adjacent flows

- `postV1Coverage_request` — `POST /v1/coverage_request`, a broader pharmacy-benefit coverage
  determination, resolving as a `COVERAGE` webhook event.
- `postV1Prior_authorization` — `POST /v1/prior_authorization`, resolving as a `PRIOR_AUTH` event.
- `getV1Pharmacycoverage` — `GET /v1/pharmacy-coverage`, picks the best-suited pharmacy location for
  the patient's benefit.

## Retry discipline

There is no idempotency key. If `postV1Copay_request` times out, **poll instead of resending**:
match on your `metadata` string in the copay webhook feed. A resubmission that reaches the server
returns `DUPLICATE_REQUEST`, which is confirmation the first one landed — not an error to correct.

## References

- `errors/truepill-decline-codes.yml` — copay rejection and CT001–CT010 triage codes
- `errors/truepill-problem-types.yml` — the HTTP error envelope
- `conventions/truepill-conventions.yml` — pagination, filtering operators, metadata correlation
- `asyncapi/truepill-webhooks.yml` — COPAY, COVERAGE and PRIOR_AUTH event shapes
