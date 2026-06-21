# Truepill (truepill)

Truepill is a pharmacy and healthcare-infrastructure company providing API-driven prescription fulfillment, pharmacy dispensing, insurance/copay adjudication, telehealth, and at-home diagnostics. Following LetsGetChecked's 2024 acquisition of Truepill, the combined company rebranded as Fuze Health in May 2025, and the developer platform now ships as FuzeRx. The REST API exposes JSON endpoints for patients, prescriptions, transfers, insurance/copay, and webhook events under https://rxapi.fuzehealth.com/v1.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/truepill/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/truepill/refs/heads/main/apis.yml)

## Tags

- Pharmacy
- Healthcare
- Prescription Fulfillment
- Telehealth
- Diagnostics
- Insurance

## Timestamps

- **Created:** 2026-06-21
- **Modified:** 2026-06-21

## APIs

### Truepill Patients API

Create, update, retrieve, and search patient records (demographics, address, contact, language preference) and list a patient's prescriptions. Patients are referenced by an opaque patient_token.

- **Human URL:** [https://rxdocs.fuzehealth.com](https://rxdocs.fuzehealth.com)
- **Base URL:** `https://rxapi.fuzehealth.com/v1`

#### Tags

- Patients
- Demographics
- Eligibility

#### Properties

- [Documentation](https://rxdocs.fuzehealth.com)
- [OpenAPI](openapi/truepill-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/truepill.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/truepill.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Truepill Prescriptions & Orders API

Retrieve prescription details and orchestrate prescription routing via transfer requests and direct transfers (v1 and v2) into Truepill/FuzeRx pharmacy fulfillment.

- **Human URL:** [https://rxdocs.fuzehealth.com](https://rxdocs.fuzehealth.com)
- **Base URL:** `https://rxapi.fuzehealth.com/v1`

#### Tags

- Prescriptions
- Orders
- Transfers

#### Properties

- [Documentation](https://rxdocs.fuzehealth.com)
- [OpenAPI](openapi/truepill-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/truepill.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/truepill.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Truepill Medications & Insurance API

Establish patient insurance objects (BIN, group, member ID), submit copay requests for real-time out-of-pocket determination, and query adjudicated insurance claim details across primary, secondary, and tertiary coverage.

- **Human URL:** [https://rxdocs.fuzehealth.com](https://rxdocs.fuzehealth.com)
- **Base URL:** `https://rxapi.fuzehealth.com/v1`

#### Tags

- Medications
- Insurance
- Copay
- Eligibility

#### Properties

- [Documentation](https://rxdocs.fuzehealth.com)
- [API Reference](https://tpos-api.truepill.com/documentation/)
- [OpenAPI](openapi/truepill-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/truepill.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/truepill.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Truepill Shipments & Fulfillment API

Pharmacy dispensing and prescription fulfillment, surfaced through prescription status and webhook events rather than a standalone shipments resource. Shipment and tracking state is delivered asynchronously via webhook notifications.

- **Human URL:** [https://healthbrands.truepill.com/](https://healthbrands.truepill.com/)
- **Base URL:** `https://rxapi.fuzehealth.com/v1`

#### Tags

- Shipments
- Fulfillment
- Dispensing

#### Properties

- [Documentation](https://rxdocs.fuzehealth.com)
- [OpenAPI](openapi/truepill-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/truepill.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/truepill.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Truepill Webhooks API

Retrieve webhook events by type (including Notify Rx) carrying asynchronous status updates for prescriptions, copay/insurance adjudication, and fulfillment. Events include callback_type, status, timestamp, and a details object.

- **Human URL:** [https://rxdocs.fuzehealth.com](https://rxdocs.fuzehealth.com)
- **Base URL:** `https://rxapi.fuzehealth.com/v1`

#### Tags

- Webhooks
- Events
- Notifications

#### Properties

- [Documentation](https://rxdocs.fuzehealth.com)
- [OpenAPI](openapi/truepill-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/truepill.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/truepill.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/truepill)
- [Website](https://www.truepill.com)
- [Documentation](https://rxdocs.fuzehealth.com)
- [Plans](plans/truepill-plans-pricing.yml)
- [Rate Limits](rate-limits/truepill-rate-limits.yml)
- [Fin Ops](finops/truepill-finops.yml)

## Ownership

LetsGetChecked acquired Truepill in 2024. In May 2025 the combined company launched as **Fuze Health** (with Alto Pharmacy joining), and the developer platform now ships as **FuzeRx**. Legacy `truepill.com/api-docs` URLs 301-redirect to `rx.fuzehealth.com`, and the API is served from `rxapi.fuzehealth.com`. Production access and pricing are sales-led, so plans, rate limits, and FinOps artifacts are marked `reconciled: false`. See [review.yml](review.yml) for the full availability and ownership assessment.

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
