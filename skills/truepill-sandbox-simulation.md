---
name: Run a FuzeRx integration end-to-end in the sandbox
description: Use the FuzeRx sandbox host and its simulation object to manufacture a prescription and drive the full asynchronous webhook lifecycle without a real prescriber, PBM or Surescripts transmission.
api: openapi/truepill-patients-api-openapi.yml, openapi/truepill-consults-api-openapi.yml
generated: '2026-08-15'
method: generated
source: sandbox/truepill-sandbox.yml + https://rxdocs.fuzehealth.com (Environments, Sandbox Testing with Simulations)
operations:
  - putV1Patient
  - getV1PatientPatient_tokenPrescriptions
  - postV1Fill_request
  - getV1Webhook_eventsWebhook_type
  - patchConsultsV0ConsultConsult_idSimulate
---

# Run a FuzeRx integration end-to-end in the sandbox

## The two environments

| | Production | Sandbox |
|---|---|---|
| Host | `https://rxapi.fuzehealth.com` | `https://rxapi.sandbox.fuzehealth.com` |
| Key prefix | `tp_live_key_` | `tp_test_key_` |
| Live pharmacy / PBM / Surescripts | yes | no |
| Swagger | `/swagger.json` | `/swagger.json` |

The environment is selected purely by **which key you send and which host you call**. There is no
environment header and no mode flag in the body. Tokens are environment-scoped — a sandbox
`patient_token` returns `404` in production.

Both keys are provisioned by Fuze Health during commercial onboarding. There is **no self-serve
sandbox signup**; contact rx.support@fuzehealth.com.

## Why simulation exists

The sandbox has no e-prescribing behind it. Nothing arrives over Surescripts, so no prescription
will ever appear on a sandbox patient by itself, and the entire fill / copay / transfer surface has
nothing to operate on. The `simulation` object is how you manufacture the missing upstream.

## Manufacture a patient and a prescription in one call

`putV1Patient` — `PUT /v1/patient`, with a `simulation` object in the body.

`simulation.newRx` takes the shape `[true, { ...prescription attributes... }]`. It creates the
patient **and** a simulated electronic prescription attached to them, which fires a `NOTIFY_RX`
webhook event exactly as a real eScript would.

Documented prescription attributes you can set:

`medication_sig`, `prescriber`, `days_supply`, `num_refills_filled`, `refills_remaining`,
`quantity_remaining`, `number_of_refills_allowed`, `prescribed_drug_strength`, `prescribed_ndc`,
`prescribed_quantity`, `prescribed_unit_text`, `prescribed_label_type`, `prescribed_brand_name`,
`prescribed_written_name`, `prescribed_generic_name`, `dispensed_drug_strength`, `dispensed_ndc`,
`dispensed_quantity`, `dispensed_days_supply`.

## Then run the real flow

1. `getV1PatientPatient_tokenPrescriptions` — confirm the simulated prescription is there and
   `fillable`.
2. `postV1Fill_request` — submit it exactly as you would in production.
3. `getV1Webhook_eventsWebhook_type` — watch the `ORDER`, then `SHIPMENT`, events arrive.

Simulation is documented as covering: Patient & Prescription, Copay request, Fill request, Direct
Transfer, and Consult. For consults there is a dedicated endpoint —
`patchConsultsV0ConsultConsult_idSimulate`, `PATCH /consults/v0/consult/{consult_id}/simulate` —
which advances a consult's status without a clinician.

## Exercise the failures too

The sandbox carries **simulation error events**, so the rejection and triage codes can be triggered
deliberately instead of waited for. Test at minimum:

- `R1` (duplicate order) — so your handler learns that R1 means the *first* request succeeded
- a `triage` status on `ORDER` and on `COPAY` — the state most integrations forget exists
- `TRANSFER_DUPLICATE` and `DUPLICATE_REQUEST` — the API's substitute for idempotency

## What the sandbox will NOT give you

- **No published fixture catalogue.** There are no magic test NDCs, no reserved patient tokens, no
  test card numbers. Everything is generated per simulation run, so you cannot hard-code fixtures
  into a test suite the way you would with a payments API — generate them each run.
- **No test clocks** or time simulation. Anything time-dependent (auto-refill schedules, expiry)
  cannot be fast-forwarded.
- **No rate-limit behaviour to observe** — nothing is published or signalled in headers.

Configure a separate test webhook endpoint in the FuzeRx Dashboard (gear icon → Profile → Account
Details) and use its "test the integration" action before you rely on delivery.

## References

- `sandbox/truepill-sandbox.yml` — the full sandbox record
- `authentication/truepill-authentication.yml` — key prefixes and issuance
- `asyncapi/truepill-webhooks.yml` — what you should see arrive
- `errors/truepill-error-codes.yml`, `errors/truepill-decline-codes.yml` — what to simulate
