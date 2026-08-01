---
name: Create a rental order in Rentle (Twice Commerce)
description: Look up a product and store, then create a rental order for a customer via the Twice Admin API.
api: openapi/rentle-admin-openapi.json
operations: [products_list, store_list, customers_search, orders_create, orders_get]
---

# Create a rental order

Use the Twice Admin API to place a rental order programmatically.

## Prerequisites
- An API key (id + secret) from the Admin UI Integrations page (admin.rentle.io/account/integrations).
- Authenticate with HTTP Basic: username = API key id, password = API key secret.
- Send `X-Rentle-Version: 2023-02-01` on every request.
- Base URL: `https://api.twicecommerce.com/admin`.

## Steps
1. Find the store to fulfil from with `store_list` (`GET /stores`); note the store id.
2. Find the product to rent with `products_list` (`GET /products`); note the product / SKU ids.
3. Find or confirm the customer with `customers_search` (`GET /customers/search`); note the customer id (or include customer details inline on the order).
4. Create the order with `orders_create` (`POST /orders`) using the `CreateOrderDTO` body: store, customer, and the rented product line(s) with rental period.
5. Read back the created order with `orders_get` (`GET /orders/{id}`) to confirm status.

## Rules
- Rate limit: leaky bucket (capacity 50, 1 request/second) — back off on `429 RateLimitExceedError`.
- No idempotency key is supported; do not blindly retry `POST /orders` on timeouts — re-check with `orders_list`/`orders_get` first.
- Errors come back as `{ "error": { status, code, message, details[] } }`; inspect `details[]` for field-level validation problems on `400`.
