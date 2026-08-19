---
name: Transfer a prescription into FuzeRx from another pharmacy
description: Locate the losing pharmacy, request a pharmacy-to-pharmacy transfer, and follow the TRANSFER and TRANSFER_CONTACT events — including the direct-transfer (RxForward) path and its v1/v2 fork.
api: openapi/truepill-transfers-api-openapi.yml, openapi/truepill-prescriptions-api-openapi.yml
generated: '2026-08-15'
method: generated
source: openapi/truepill-transfers-api-openapi.yml + https://rxdocs.fuzehealth.com
operations:
  - getV1External_locations
  - getV1External_location_matches
  - postV1External_location_matches
  - getV1External_locationsId
  - postV1Transfer_request
  - getV1Transfer_requestToken
  - getV1Transfer_request
  - postV1Direct_transfer
  - postV2Direct_transfer
  - getV1Direct_transferDirect_transfer_token
  - getV1Direct_transferDirect_transfer_tokenWebhook_events
  - postV1PrescriptionPrescription_tokenTransfer_out
---

# Transfer a prescription into FuzeRx

Base URL `https://rxapi.fuzehealth.com`. Header `Authorization: ApiKey <YOUR API KEY>`.

A transfer moves an existing prescription from another pharmacy into the FuzeRx pharmacy so it can
be filled. It involves a **third party who is not on your API** — the losing pharmacy — so it is
slow, partially manual, and it fails in ways no request validation can prevent.

## Steps

### 1. Identify the losing pharmacy

Three ways, in increasing precision:

- `getV1External_locations` — `GET /v1/external_locations`, search by zip code.
- `getV1External_location_matches` — `GET /v1/external_location_matches`, match one address.
- `postV1External_location_matches` — `POST /v1/external_location_matches`, match many addresses in
  one call and get back a map. Use this for bulk onboarding.
- `getV1External_locationsId` — `GET /v1/external_locations/{id}`, read one back.

Getting this wrong is the leading cause of transfer failure: FuzeRx will contact the pharmacy you
named, and if it is the wrong branch nobody there has the prescription.

### 2. Request the transfer

`postV1Transfer_request` — `POST /v1/transfer_request`

Reference the `patient_token` and the identified external location. Set your own `metadata` string.
Returns a `transfer_token`.

### 3. Follow it

- `getV1Transfer_requestToken` — `GET /v1/transfer_request/{token}`
- `getV1Transfer_request` — `GET /v1/transfer_request`, the list
- `TRANSFER` webhook event — `status` `success` or `error`
- `TRANSFER_CONTACT` webhook event — emitted when FuzeRx contacts the losing pharmacy or the
  prescriber, carrying `contact_name`, `contact_method` and `contact_number`. This is your only
  visibility into the manual leg. Surface it to the patient rather than leaving them in silence.

`TRANSFER_DUPLICATE` in an error event means a transfer for that prescription is **already in
flight**. Do not submit again.

## The direct-transfer path (RxForward / RxTransfer)

A separate, faster surface where the transfer is pushed rather than requested:

- `postV1Direct_transfer` — `POST /v1/direct_transfer` → `DIRECT_TRANSFER` event
- `postV2Direct_transfer` — `POST /v2/direct_transfer` → `DIRECT_TRANSFER_V2` event
- `getV1Direct_transferDirect_transfer_token` — read one back by `direct_transfer_token`
- `getV1Direct_transferDirect_transfer_tokenWebhook_events` — replay that transfer's events

**Choose deliberately.** Both v1 and v2 are live and documented, and the provider publishes **no**
statement that v1 is deprecated and no sunset date — the two emit different `callback_type` values,
so your webhook handler must know which one you called. Prefer v2 for new integrations and handle
both event types if you have existing v1 traffic.

## Transferring out

`postV1PrescriptionPrescription_tokenTransfer_out` — `POST /v1/prescription/{prescription_token}/transfer_out`

Sends a prescription the other way. After this the prescription is no longer `fillable` at FuzeRx.
There is no undo.

## Retry discipline

No idempotency key. A timed-out `postV1Transfer_request` should be resolved by listing
`getV1Transfer_request` and matching your `metadata`, not by resending. `TRANSFER_DUPLICATE` is
confirmation the first attempt landed.

## References

- `errors/truepill-error-codes.yml` — transfer error codes
- `asyncapi/truepill-webhooks.yml` — TRANSFER, TRANSFER_CONTACT, DIRECT_TRANSFER, DIRECT_TRANSFER_V2
- `conventions/truepill-conventions.yml` — tokens and metadata correlation
- `lifecycle/truepill-lifecycle.yml` — why the v1/v2 fork has no published resolution
