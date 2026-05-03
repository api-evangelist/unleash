# Unleash

Unleash is the open-source feature management platform enabling progressive delivery, A/B testing, and canary releases through feature flags (feature toggles). Teams use Unleash to decouple code deployments from feature releases, control rollouts by user segment, run experiments with variants, and maintain kill switches for rapid incident response.

**Website:** [getunleash.io](https://www.getunleash.io)
**Documentation:** [docs.getunleash.io](https://docs.getunleash.io)
**GitHub:** [github.com/Unleash/unleash](https://github.com/Unleash/unleash)

---

## APIs

Unleash exposes three distinct APIs for different use cases:

### Unleash Admin API (v7.4.1)
Full programmatic control over Unleash: 255 endpoints covering feature flags, projects, environments, users, API tokens, strategies, segments, addons, events, change requests, and instance administration.
- Base URL: `https://{your-unleash-instance}/api/admin`
- Auth: `Authorization: Bearer {admin-token}`
- [OpenAPI Spec](openapi/unleash-admin-api-openapi.yml)

### Unleash Client API (v7.4.1)
Backend SDK endpoint for fetching complete flag configurations with strategies for server-side evaluation.
- Base URL: `https://{your-unleash-instance}/api/client`
- Auth: `Authorization: {backend-token}`
- [OpenAPI Spec](openapi/unleash-client-api-openapi.yml)

### Unleash Frontend API (v7.4.1)
Client-side SDK endpoint returning only evaluated flag states for a given context.
- Base URL: `https://{your-unleash-instance}/api/frontend`
- Auth: `Authorization: {frontend-token}`
- [OpenAPI Spec](openapi/unleash-frontend-api-openapi.yml)

---

## Artifacts

### OpenAPI
| File | Description |
|---|---|
| [unleash-admin-api-openapi.yml](openapi/unleash-admin-api-openapi.yml) | Admin API (255 endpoints, 263+ schemas) |
| [unleash-client-api-openapi.yml](openapi/unleash-client-api-openapi.yml) | Client API (5 endpoints) |
| [unleash-frontend-api-openapi.yml](openapi/unleash-frontend-api-openapi.yml) | Frontend API (3 endpoints) |

### Spectral Rules
| File | Description |
|---|---|
| [unleash-rules.yml](rules/unleash-rules.yml) | Spectral ruleset enforcing Unleash API conventions |

### Capabilities (Naftiko)
| File | Description |
|---|---|
| [shared/unleash-admin.yaml](capabilities/shared/unleash-admin.yaml) | Shared Admin API consumed definition |
| [shared/unleash-client.yaml](capabilities/shared/unleash-client.yaml) | Shared Client API consumed definition |
| [feature-flag-management.yaml](capabilities/feature-flag-management.yaml) | Feature flag lifecycle workflow (11 tools) |
| [access-management.yaml](capabilities/access-management.yaml) | User and token management workflow (4 tools) |

### JSON Schema
| File | Description |
|---|---|
| [unleash-feature-flag-schema.json](json-schema/unleash-feature-flag-schema.json) | Feature Flag entity schema |

### JSON Structure
| File | Description |
|---|---|
| [unleash-feature-flag-structure.json](json-structure/unleash-feature-flag-structure.json) | Feature Flag field documentation |

### JSON-LD Context
| File | Description |
|---|---|
| [unleash-context.jsonld](json-ld/unleash-context.jsonld) | Linked data context for Unleash domain concepts |

### Examples
| File | Description |
|---|---|
| [unleash-create-feature-flag-example.json](examples/unleash-create-feature-flag-example.json) | Create feature flag request/response |
| [unleash-toggle-feature-example.json](examples/unleash-toggle-feature-example.json) | Enable feature flag in environment |
| [unleash-client-features-example.json](examples/unleash-client-features-example.json) | Client SDK feature fetch |

### Vocabulary
| File | Description |
|---|---|
| [unleash-vocabulary.yml](vocabulary/unleash-vocabulary.yml) | Domain vocabulary for Unleash platform concepts |

---

## APIs Index

- [apis.yml](apis.yml)

---

*Maintained by [API Evangelist](https://apievangelist.com)*
