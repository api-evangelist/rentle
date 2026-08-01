---
name: Subscribe to Rentle order and product webhooks
description: Register webhooks for order and product lifecycle events on the Twice Admin API and verify delivery.
api: openapi/rentle-admin-openapi.json
operations: [webhooks_create, webhooks_list, webhooks_get, webhooks_remove]
---

# Subscribe to webhooks

Receive server-to-server notifications for order and product events.

## Prerequisites
- API key (HTTP Basic: id = username, secret = password) and `X-Rentle-Version: 2023-02-01`.
- A public HTTPS endpoint that returns a `2xx` within 10 seconds.
- Base URL: `https://api.twicecommerce.com/admin`.

## Steps
1. Create a webhook with `webhooks_create` (`POST /webhooks`) using `CreateWebhookDTO`: `label`, `urls[]`, `event`, `apiVersion: "2023-02-01"`. One webhook per event.
2. Choose the `event` from the supported enum:
   `order/created`, `order/updated`, `order/prepared`, `order/started`, `order/returned`, `order/returning-soon`, `product/created`, `product/updated`, `product/deleted`.
   For `order/returning-soon`, set `configuration.triggerBeforeMinutes`.
3. List existing hooks with `webhooks_list` (`GET /webhooks`) and confirm with `webhooks_get` (`GET /webhooks/{id}`).
4. Remove a hook with `webhooks_remove` (`DELETE /webhooks/{id}`) when no longer needed.

## Rules
- Delivered requests carry an `x-rentle-version` header matching the webhook's `apiVersion`.
- Order events deliver an `OrderWebhookDTO`; product events deliver a `ProductWebhookDTO`.
- Acknowledge fast (return the `2xx` before long-running work); non-2xx or timeout (>10s) counts as a delivery failure.
