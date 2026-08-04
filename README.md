# Truepill (truepill)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
