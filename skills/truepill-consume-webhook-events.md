---
name: Consume and reconcile FuzeRx webhook events
description: Receive FuzeRx webhook events, and — because there is no signature to verify and no documented retry policy — reconcile every asynchronous outcome by pulling the cursor-paginated event feed.
api: openapi/truepill-webhooks-api-openapi.yml
generated: '2026-08-15'
method: generated
source: openapi/truepill-webhooks-api-openapi.yml + https://rxdocs.fuzehealth.com
operations:
  - getV1Webhook_events
  - getV1Webhook_eventsWebhook_type
  - getV1Fill_requestUrl_tokenWebhook_events
  - getV1Direct_transferDirect_transfer_tokenWebhook_events
---

# Consume and reconcile FuzeRx webhook events

Base URL `https://rxapi.fuzehealth.com`. Header `Authorization: ApiKey <YOUR API KEY>`.

**FuzeRx is asynchronous by design.** A 200 or 202 on a POST means *accepted*. The actual outcome —
was the prescription dispensed, what does the patient owe, did the transfer land — arrives later as
a webhook event. If you are not consuming events, you are not integrated.

## The two hard facts about this surface

1. **Events are not signed.** No signature header, no shared secret, no verification procedure is
   documented. You cannot cryptographically prove an inbound POST came from FuzeRx, and the payloads
   carry PHI-adjacent tokens. Mitigate outside the API: restrict your endpoint by source IP where
   your platform allows, use an unguessable endpoint path, and **treat the pull feed as the system
   of record** — confirm every consequential event by reading it back before you act on it.
2. **There is no documented retry or backoff policy.** A delivery you miss may simply never arrive
   again. The pull feed is the only stated recovery path, so poll it on a schedule regardless of
   whether deliveries look healthy.

## Configure the endpoint

Production and test webhook endpoints are set in the FuzeRx Dashboard under the gear icon →
Profile → Account Details, which also offers a "test the integration" action. Test and production
endpoints are configured separately.

## The event envelope

Every event except `NOTIFY_RX` carries:

| Field | Type | Notes |
|---|---|---|
| `request_id` | string | Token of the originating request, e.g. `fill_request_991e90fa6b367cf72032` |
| `callback_type` | string | The event type |
| `status` | string | Usually `success` or `error`; also `triage` on `ORDER` and `COPAY` |
| `details` | object | Type-specific payload |
| `timestamp` | integer | Unix epoch seconds |

`details.metadata` echoes the string you supplied on the originating request. **That is your join
key** — `request_id` alone will not correlate a lifecycle that spans several objects.

`NOTIFY_RX` is the exception: it has no `request_id` and no `status`, because it is not the outcome
of anything you asked for — it fires when a prescription arrives at the pharmacy over Surescripts.

## Pull the feed

`getV1Webhook_eventsWebhook_type` — `GET /v1/webhook_events/{webhook_type}`

Returns `results[]`, capped at **100** entries per page (not configurable). When more exist the
response carries `next_page_token`; pass it back as the `next` query parameter.

Filter with operator-prefixed parameters on `id`, `timestamp`, `next` and `status`:

```
GET /v1/webhook_events/notify_rx?timestamp=$gt:1625177120&timestamp=$lt:1625177295
```

Operators are `$lt`, `$gt`, `$lte`, `$gte`, `$eq`. (The published docs table lists `$lte` twice; the
second row is a typo for `$gte`.)

Scoped variants when you know the object:

- `getV1Fill_requestUrl_tokenWebhook_events` — `GET /v1/fill_request/{url_token}/webhook_events`
- `getV1Direct_transferDirect_transfer_tokenWebhook_events` —
  `GET /v1/direct_transfer/{direct_transfer_token}/webhook_events`
- `getV1Webhook_events` — `GET /v1/webhook_events`, everything

## A working reconciliation loop

1. Persist the highest `timestamp` you have processed, per `webhook_type`.
2. On each poll, request `timestamp=$gt:<high-water mark>` and page through `next_page_token` until
   it is absent.
3. Deduplicate on `request_id` + `callback_type` + `timestamp` — a pulled event and a delivered
   event are the same event, and you will see both.
4. Advance the high-water mark only after the page is fully processed.

## The twelve event types

`NOTIFY_RX`, `ORDER`, `SHIPMENT`, `TRANSFER`, `TRANSFER_CONTACT`, `DIRECT_TRANSFER`,
`DIRECT_TRANSFER_V2`, `COPAY`, `COVERAGE`, `PRIOR_AUTH`, `CONSULT`, `FILE`.

The documentation's overview list names only nine; `TRANSFER_CONTACT`, `DIRECT_TRANSFER_V2` and
`FILE` appear only inside individual product sections. Handle an unknown `callback_type` by logging
and skipping, never by erroring — this list has grown without announcement, and there is no
changelog to warn you.

## References

- `asyncapi/truepill-webhooks.yml` — the full catalogue, per-event `details` fields
- `errors/truepill-error-codes.yml` — order and transfer rejection/triage codes
- `errors/truepill-decline-codes.yml` — copay rejection and triage codes
- `conventions/truepill-conventions.yml` — pagination, filter operators, correlation
