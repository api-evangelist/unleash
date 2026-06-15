# Unleash (unleash)

Unleash is the open-source feature management platform enabling progressive delivery, A/B testing, and canary releases through feature flags (feature toggles). Teams use Unleash to decouple code deployments from feature releases, control rollouts by user segment, run experiments with variants, and maintain kill switches for rapid incident response. Available as open-source (self-hosted) and as Unleash Cloud (managed).

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- Feature Flags
- Feature Management
- Progressive Delivery
- A/B Testing
- Open Source
- Developer Tools

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-05-19

## APIs

### Unleash Admin API

Comprehensive REST API providing full programmatic control over Unleash resources including feature flags, projects, environments, users, API tokens, strategies, segments, integrations (addons), events, change requests, and instance administration. Powers the Unleash Admin UI and enables CI/CD and GitOps automation workflows. Base URL: https://{your-unleash-instance}/api/admin

- **Human URL:** [https://docs.getunleash.io/api](https://docs.getunleash.io/api)
- **Base URL:** `https://app.unleash-instance.example.com`

#### Tags

- Admin
- Feature Flags
- Projects
- Environments
- Users
- Events

#### Properties

- [Documentation](https://docs.getunleash.io/api)
- [OpenAPI](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/openapi/unleash-admin-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Spectral Rules](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/rules/unleash-rules.yml)
- [Postman Collection](collections/unleash-admin-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-admin-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-client-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-client-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-frontend-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-frontend-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Unleash Client API

API endpoint consumed by backend Unleash SDKs to fetch complete feature flag configurations including all activation strategies. Supports server-side flag evaluation with zero additional latency after initial bootstrap. Requires a backend API token scoped to a project and environment.

- **Human URL:** [https://docs.getunleash.io/api#client-api](https://docs.getunleash.io/api#client-api)
- **Base URL:** `https://app.unleash-instance.example.com`

#### Tags

- Client
- SDK
- Feature Flags

#### Properties

- [Documentation](https://docs.getunleash.io/api)
- [OpenAPI](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/openapi/unleash-client-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/unleash-admin-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-admin-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-client-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-client-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-frontend-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-frontend-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Unleash Frontend API

API endpoint consumed by frontend/client-side SDKs. Returns only the evaluated state (enabled/disabled) of feature flags for a given Unleash Context, without exposing strategy configuration. Safe for use in browser and mobile apps.

- **Human URL:** [https://docs.getunleash.io/api#frontend-api](https://docs.getunleash.io/api#frontend-api)
- **Base URL:** `https://app.unleash-instance.example.com`

#### Tags

- Frontend
- SDK
- Feature Flags

#### Properties

- [Documentation](https://docs.getunleash.io/api)
- [OpenAPI](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/openapi/unleash-frontend-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/unleash-admin-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-admin-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-client-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-client-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/unleash-frontend-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/unleash-frontend-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/getunleash)
- [Website](https://www.getunleash.io)
- [Documentation](https://docs.getunleash.io)
- [Git Hub](https://github.com/Unleash/unleash)
- [Pricing](https://www.getunleash.io/pricing)
- [Blog](https://www.getunleash.io/blog)
- [Changelog](https://github.com/Unleash/unleash/releases)
- [SDK](https://docs.getunleash.io/reference/sdks)
- [Vocabulary](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/vocabulary/unleash-vocabulary.yml)
- [J S O N L D Context](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/json-ld/unleash-context.jsonld)
- [JSON Schema](https://raw.githubusercontent.com/api-evangelist/unleash/refs/heads/main/json-schema/unleash-feature-flag-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [Integrations](https://www.getunleash.io/partners)
- [L L Ms Txt](https://docs.getunleash.io/llms.txt)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
