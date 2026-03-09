# 🎨 Themes API

[![Fastify](https://img.shields.io/badge/Fastify-5-000000?logo=fastify&logoColor=white)](https://github.com/fastify/fastify)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?logo=typescript&logoColor=white)](https://github.com/microsoft/TypeScript)
[![Node.js](https://img.shields.io/badge/Node.js-24-5FA04E?logo=nodedotjs&logoColor=white)](https://github.com/nodejs/node)
[![Zod](https://img.shields.io/badge/Zod-3-3068B7?logo=zod&logoColor=white)](https://github.com/colinhacks/zod)
[![Pino](https://img.shields.io/badge/Pino-9-687634?logo=pino&logoColor=white)](https://github.com/pinojs/pino)
[![pnpm](https://img.shields.io/badge/pnpm-10-F69220?logo=pnpm&logoColor=white)](https://github.com/pnpm/pnpm)
[![ESLint](https://img.shields.io/badge/ESLint-10-4B32C3?logo=eslint&logoColor=white)](https://github.com/eslint/eslint)
[![Prettier](https://img.shields.io/badge/Prettier-3-F7B93E?logo=prettier&logoColor=black)](https://github.com/prettier/prettier)
[![Vitest](https://img.shields.io/badge/Vitest-3-6E9F18?logo=vitest&logoColor=white)](https://github.com/vitest-dev/vitest)
[![Docker](https://img.shields.io/badge/Docker-ready-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![All Rights Reserved](https://img.shields.io/badge/License-All%20Rights%20Reserved-red.svg)](LICENSE)
[![GitHub](https://img.shields.io/badge/GitHub-scottdreinhart%2Fthemes-api-181717?logo=github&logoColor=white)](https://github.com/scottdreinhart/themes-api)

Fastify API backend for theme catalog management, DLC distribution, and purchase fulfillment across the game portfolio

**⚠️ PROPRIETARY SOFTWARE — All Rights Reserved**

© 2026 Scott Reinhart. This software is proprietary and confidential.
Unauthorized reproduction, distribution, or use is strictly prohibited.
See [LICENSE](LICENSE) file for complete terms and conditions.

> [!CAUTION]
> **LICENSE TRANSITION PLANNED** — This project is currently proprietary. The license will change to open source once the project has reached a suitable state to allow for it.

[Project Structure](#project-structure) · [Getting Started](#getting-started) · [Tech Stack](#tech-stack) · [API Reference](#api-reference) · [Architecture](#architecture) · [Environment Variables](#environment-variables) · [Authentication](#authentication) · [Error Handling](#error-handling) · [Rate Limiting](#rate-limiting) · [Data Models](#data-models) · [Deployment](#deployment) · [Supported Clients](#supported-clients) · [Versioning](#versioning) · [Remaining Work](#remaining-work) · [Future Improvements](#future-improvements) · [Contributing](#contributing) · [Portfolio Services](#portfolio-services) · [Portfolio Games](#portfolio-games)

## Project Structure

```
src/
├── domain/                           # Pure, framework-agnostic logic
│   ├── types.ts                      # Central type definitions (theme packs, catalog items, asset manifests, download tokens, purchase records, and install states)
│   ├── errors.ts                     # Domain error classes (NotFoundError, ValidationError, etc.)
│   ├── constants.ts                  # Domain constants (page sizes, timeouts)
│   ├── schemas.ts                    # Zod validation schemas (pagination, ID params)
│   └── index.ts                      # Barrel export — re-exports all domain modules
├── app/
│   ├── app.ts                        # Fastify instance builder (plugins, routes, middleware)
│   └── index.ts                      # Barrel export — re-exports app builder
├── infra/
│   ├── config.ts                     # Environment configuration (validates env vars)
│   ├── logger.ts                     # Pino structured logger (standalone, non-request)
│   └── index.ts                      # Barrel export — re-exports infra modules
├── routes/
│   ├── health.ts                     # GET /health + GET /ready system endpoints
│   └── index.ts                      # Barrel export — re-exports all route modules
├── __tests__/
│   └── health.test.ts                # Health endpoint integration tests
└── server.ts                         # Entrypoint — starts the Fastify server

Dockerfile                            # Multi-stage production Docker image
.dockerignore                         # Docker build exclusions
.env.example                          # Environment variable template
package.json                          # Dependencies & scripts
pnpm-lock.yaml                        # pnpm lockfile
pnpm-workspace.yaml                   # pnpm workspace config
LICENSE                               # Proprietary license terms

tsconfig.json                         # TypeScript config (strict mode + @/ path aliases)
tsconfig.build.json                   # TypeScript build config (emits to dist/)
eslint.config.js                      # ESLint flat config (TypeScript + Prettier + boundary enforcement)
vitest.config.ts                      # Vitest test runner config (path aliases + coverage)
.prettierrc                           # Prettier formatting rules
.gitignore                            # Git ignore rules
.nvmrc                                # Node.js version pin (v24)
```

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v24+ (pin via [nvm](https://github.com/nvm-sh/nvm) — see `.nvmrc`)
- [pnpm](https://pnpm.io/) v10+

### Install & Run

```bash
# Install dependencies
pnpm install

# Start development server (auto-restart on file changes)
pnpm dev

# Start production server (no file watching)
pnpm start

# Build for production (compiles TypeScript to dist/)
pnpm build

# Run compiled production build
pnpm preview
```

### Code Quality

```bash
# Individual tools
pnpm lint           # ESLint — check for issues
pnpm lint:fix       # ESLint — auto-fix issues
pnpm format         # Prettier — format all source files
pnpm format:check   # Prettier — check formatting without writing
pnpm typecheck      # TypeScript type check (tsc --noEmit)

# Chains
pnpm check          # lint + format:check + typecheck in one pass (quality gate)
pnpm fix            # lint:fix + format in one pass (auto-fix everything)
pnpm validate       # check + build — full pre-push validation
```

### Testing

```bash
pnpm test           # Run all tests once
pnpm test:watch     # Watch mode — re-run on file changes
pnpm test:coverage  # Run with V8 coverage report
```

### Docker

```bash
# Build the image
docker build -t themes-api .

# Run the container
docker run -p 3000:3000 themes-api
```

### Cleanup & Maintenance

```bash
pnpm clean          # wipe dist/ build output
pnpm clean:node     # delete node_modules only
pnpm clean:all      # nuclear — dist/ + node_modules/
pnpm reinstall      # clean:all + pnpm install
```

## Tech Stack

| Technology | Version | Purpose |
|---|---|---|
| [Fastify](https://github.com/fastify/fastify) | 5 | High-performance web framework |
| [TypeScript](https://github.com/microsoft/TypeScript) | 5.9 | Static type checking (strict mode) |
| [Node.js](https://github.com/nodejs/node) | 24 | Runtime (pinned via `.nvmrc`) |
| [Zod](https://github.com/colinhacks/zod) | 3 | Runtime schema validation |
| [Pino](https://github.com/pinojs/pino) | 9 | Structured JSON logging |
| [@fastify/swagger](https://github.com/fastify/fastify-swagger) | 9 | OpenAPI spec generation |
| [@fastify/swagger-ui](https://github.com/fastify/fastify-swagger-ui) | 5 | Interactive API documentation (`/docs`) |
| [@fastify/cors](https://github.com/fastify/fastify-cors) | 11 | Cross-origin resource sharing |
| [@fastify/helmet](https://github.com/fastify/fastify-helmet) | 13 | Security headers |
| [@fastify/rate-limit](https://github.com/fastify/fastify-rate-limit) | 10 | Request rate limiting |
| [Vitest](https://github.com/vitest-dev/vitest) | 3 | Test runner (V8 coverage) |
| [ESLint](https://github.com/eslint/eslint) | 10 | Linting (flat config + boundary enforcement) |
| [Prettier](https://github.com/prettier/prettier) | 3 | Code formatting |
| [pnpm](https://github.com/pnpm/pnpm) | 10 | Fast, disk-efficient package manager |
| [Docker](https://www.docker.com/) | — | Containerized production deployment |

## API Reference

Once the server is running, interactive OpenAPI documentation is available at:

```
http://localhost:3000/docs
```

### Built-in Endpoints

| Method | Path | Description |
|---|---|---|
| `GET` | `/health` | Health check — returns status, uptime, timestamp |
| `GET` | `/ready` | Readiness check — returns `{ ready: true }` when all dependencies are available |
| `GET` | `/docs` | Swagger UI — interactive API documentation |

### Proposed Endpoints

#### Theme Catalog

| Method | Path | Description |
|---|---|---|
| `GET` | `/themes` | List all available themes (paginated, filterable by game/category/price) |
| `GET` | `/themes/:id` | Get theme details (name, description, preview images, price, compatibility) |
| `GET` | `/themes/featured` | Get featured/promoted themes for storefront display |
| `GET` | `/themes/search` | Full-text search across theme names, descriptions, and tags |
| `POST` | `/themes` | Create a new theme entry (admin) |
| `PATCH` | `/themes/:id` | Update theme metadata (admin) |
| `DELETE` | `/themes/:id` | Archive a theme from the catalog (admin) |

#### Theme Packs (Bundles)

| Method | Path | Description |
|---|---|---|
| `POST` | `/packs` | Create a theme bundle (groups multiple themes at a discounted price) |
| `GET` | `/packs` | List all theme packs |
| `GET` | `/packs/:id` | Get pack details and included themes |
| `PATCH` | `/packs/:id` | Update pack contents or pricing (admin) |
| `DELETE` | `/packs/:id` | Archive a theme pack (admin) |

#### Purchases & Entitlements

| Method | Path | Description |
|---|---|---|
| `POST` | `/purchases` | Purchase a theme or pack (validates entitlement via Billing API) |
| `GET` | `/purchases` | List user’s purchased themes (paginated) |
| `GET` | `/purchases/:id` | Get purchase receipt details |
| `GET` | `/entitlements` | List all themes a user is entitled to (purchased + free + bundled) |
| `GET` | `/entitlements/check` | Check if user owns a specific theme (`?userId=&themeId=`) |

#### Asset Downloads

| Method | Path | Description |
|---|---|---|
| `POST` | `/downloads/:themeId/token` | Generate a time-limited signed download token for theme assets |
| `GET` | `/downloads/:themeId` | Download theme asset bundle (requires valid token) |
| `GET` | `/downloads/:themeId/manifest` | Get asset manifest (file list, sizes, checksums for incremental sync) |

#### Install State

| Method | Path | Description |
|---|---|---|
| `GET` | `/installs` | List user’s installed themes per game |
| `POST` | `/installs` | Record a theme installation on a specific game client |
| `DELETE` | `/installs/:id` | Record a theme uninstall |
| `GET` | `/installs/sync` | Get install diff for a game client (what to add/remove since last sync) |

#### Theme Versions

| Method | Path | Description |
|---|---|---|
| `POST` | `/themes/:id/versions` | Publish a new version of a theme (admin) |
| `GET` | `/themes/:id/versions` | List version history for a theme |
| `GET` | `/themes/:id/versions/latest` | Get latest version metadata and changelog |

#### Categories & Tags

| Method | Path | Description |
|---|---|---|
| `POST` | `/categories` | Create a theme category (e.g., Dark, Nature, Holiday, Retro) |
| `GET` | `/categories` | List all categories with theme counts |
| `GET` | `/categories/:id` | Get category details and its themes |
| `PATCH` | `/categories/:id` | Update category name, description, or sort order (admin) |
| `DELETE` | `/categories/:id` | Archive a category (admin) |
| `POST` | `/tags` | Create a tag for cross-category labeling |
| `GET` | `/tags` | List all tags with usage counts |
| `POST` | `/themes/:id/tags` | Assign tags to a theme |
| `DELETE` | `/themes/:id/tags/:tagId` | Remove a tag from a theme |

#### Ratings & Reviews

| Method | Path | Description |
|---|---|---|
| `POST` | `/themes/:id/reviews` | Submit a rating and review for a purchased theme |
| `GET` | `/themes/:id/reviews` | List reviews for a theme (paginated, sortable by date/rating) |
| `GET` | `/themes/:id/reviews/summary` | Get aggregated rating (average, distribution, total count) |
| `PATCH` | `/reviews/:id` | Update own review |
| `DELETE` | `/reviews/:id` | Delete own review |
| `POST` | `/reviews/:id/report` | Flag a review for moderation |

#### Game Compatibility

| Method | Path | Description |
|---|---|---|
| `GET` | `/compatibility/:themeId` | List games compatible with a theme |
| `POST` | `/compatibility` | Register a theme-game compatibility mapping (admin) |
| `DELETE` | `/compatibility/:id` | Remove a compatibility mapping (admin) |
| `GET` | `/compatibility/games/:gameId` | List all themes compatible with a specific game |

#### Wishlists

| Method | Path | Description |
|---|---|---|
| `POST` | `/wishlists` | Add a theme to user's wishlist |
| `GET` | `/wishlists` | List user's wishlisted themes |
| `DELETE` | `/wishlists/:themeId` | Remove a theme from wishlist |
| `GET` | `/wishlists/notifications` | Check if any wishlisted themes are on sale or newly released |

#### Preview Assets

| Method | Path | Description |
|---|---|---|
| `POST` | `/themes/:id/previews` | Upload preview screenshots for a theme (admin) |
| `GET` | `/themes/:id/previews` | List preview images for a theme (thumbnails + full-size URLs) |
| `DELETE` | `/previews/:id` | Remove a preview image (admin) |
| `PATCH` | `/previews/:id` | Update preview sort order or alt text (admin) |

#### Analytics

| Method | Path | Description |
|---|---|---|
| `GET` | `/analytics/popular` | Most downloaded themes (filterable by game/time range) |
| `GET` | `/analytics/trending` | Trending themes (based on recent download velocity) |
| `GET` | `/analytics/top-rated` | Highest-rated themes with minimum review threshold |
| `GET` | `/analytics/revenue` | Theme sales revenue report (filterable by date range/category) |

### Client UI Pairings

Endpoints that pair directly with a visible UI element in game clients or the Theme Store admin app:

| UI Element | Icon / Control | Endpoint | Surface |
|---|---|---|---|
| Theme card grid | Thumbnail image cards in a responsive grid | `GET /themes` | Game client — theme store browse |
| Theme detail view | Full-screen preview with metadata panel | `GET /themes/:id` | Game client — theme detail |
| "Featured" hero carousel | Image carousel / banner slider | `GET /themes/featured` | Game client — theme store landing |
| Search bar | 🔍 magnifying glass icon + text input | `GET /themes/search` | Game client — theme store header |
| Category filter bar | Pill / chip buttons (Dark, Nature, Holiday, etc.) | `GET /categories` | Game client — theme store sidebar or top bar |
| Tag badges | Colored tag chips on theme cards | `GET /tags` | Game client — theme card overlay |
| "Buy" button | 🛒 shopping cart icon CTA on theme card | `POST /purchases` | Game client — theme card / detail |
| Price badge | 💲 price tag label on theme card | `GET /themes/:id` | Game client — theme card corner |
| "Download" button | ⬇ download arrow (appears after purchase) | `GET /downloads/:themeId` | Game client — owned theme card |
| Download progress bar | Progress indicator during download | `GET /downloads/:themeId/manifest` | Game client — download overlay |
| "Apply Theme" button | 🎨 paintbrush icon | `POST /installs` | Game client — owned theme card |
| "Remove Theme" button | 🗑 trash icon on installed theme row | `DELETE /installs/:id` | Game client — installed themes list |
| Active theme indicator | ✅ checkmark badge on current theme | `GET /installs` | Game client — theme list |
| Wishlist heart toggle | ❤ heart icon (outline ↔ filled) | `POST /wishlists` · `DELETE /wishlists/:themeId` | Game client — theme card corner |
| Wishlist sale notification | 🔔 bell badge on wishlisted theme | `GET /wishlists/notifications` | Game client — theme store notification dot |
| Star rating widget | ⭐ 1–5 clickable stars | `POST /themes/:id/reviews` | Game client — theme detail (post-purchase) |
| Rating summary | ⭐ average + histogram bars | `GET /themes/:id/reviews/summary` | Game client — theme detail header |
| Review text area | Text input + "Submit" button | `POST /themes/:id/reviews` | Game client — review form |
| Review edit button | ✏ pencil icon on own review | `PATCH /reviews/:id` | Game client — own review row |
| Review delete button | 🗑 trash icon on own review | `DELETE /reviews/:id` | Game client — own review row |
| Report review button | 🚩 flag icon on review row | `POST /reviews/:id/report` | Game client — review row |
| Compatibility badges | ✅ / ❌ per-game icons on theme card | `GET /compatibility/:themeId` | Game client — theme detail |
| Preview image carousel | Image lightbox / swipe gallery | `GET /themes/:id/previews` | Game client — theme detail |
| Version selector dropdown | Dropdown showing version history | `GET /themes/:id/versions` | Admin app — theme detail |
| "Update Available" badge | 🔄 update arrow badge on theme card | `GET /themes/:id/versions/latest` | Game client — installed themes |
| Bundle card | Multi-theme pack card with discount badge | `GET /packs` | Game client — theme store bundles section |
| "Trending" badge | 🔥 fire icon on theme card | `GET /analytics/trending` | Game client — theme store |
| "Popular" badge | 📈 chart icon on theme card | `GET /analytics/popular` | Game client — theme store |
| Top-rated filter | ⭐ "Top Rated" sort button | `GET /analytics/top-rated` | Game client — theme store toolbar |
| Revenue chart | 📊 bar/line chart widget | `GET /analytics/revenue` | Admin app — analytics dashboard |

## Architecture

This project enforces seven complementary design principles:

1. **CLEAN Architecture** (Layer Separation)
   - `domain/` layer: Pure types, errors, constants, schemas (zero framework dependencies)
   - `app/` layer: Fastify instance builder, use-case services
   - `infra/` layer: External adapters (database, cache, third-party APIs)
   - `routes/` layer: HTTP route handlers (thin — delegates to app/domain)
   - **Benefit**: Domain logic is testable, reusable, and framework-independent

2. **SOLID Principles** (Code-Level Design)
   - Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion
   - **Benefit**: Code is maintainable, testable, and resistant to side effects

3. **DRY Principle** (No Duplication)
   - Shared schemas, constants, and error types eliminate duplication
   - **Benefit**: Changes propagate consistently; less code to maintain

4. **Import Boundary Enforcement** (`eslint-plugin-boundaries`)
   - `domain/` → may only import from `domain/` (zero framework deps)
   - `app/` → may import `domain/` + `app/` (never `infra/` or `routes/`)
   - `infra/` → may import `domain/`, `app/`, and `infra/`
   - `routes/` → may import `domain/`, `app/`, and `routes/`
   - **Benefit**: CLEAN layer violations are caught at lint time, not at code review

5. **Path Aliases** (`@/domain`, `@/app`, `@/infra`, `@/routes`)
   - Configured in `tsconfig.json` (`paths`) and `vitest.config.ts` (`resolve.alias`)
   - Eliminates fragile `../../` relative imports across layers
   - **Benefit**: Imports are self-documenting and resilient to file moves

6. **Barrel Exports** (`index.ts` per directory)
   - Each layer exposes a single public API via its barrel file
   - Internal module structure can change without breaking consumers
   - **Benefit**: Explicit public APIs; refactoring internals doesn't cascade import changes

7. **Schema-First Validation** (Zod + OpenAPI)
   - All inputs validated with Zod schemas in the domain layer
   - Fastify route schemas auto-generate OpenAPI docs via `@fastify/swagger`
   - **Benefit**: Single source of truth for validation, serialization, and documentation

## Environment Variables

Copy `.env.example` to `.env` and configure:

| Variable | Description | Type | Default | Required |
| -------- | ----------- | ---- | ------- | -------- |
| `PORT` | HTTP server port | `number` | `3000` | No |
| `HOST` | Bind address | `string` | `0.0.0.0` | No |
| `NODE_ENV` | Runtime environment (`development`, `staging`, `production`) | `string` | `development` | No |
| `LOG_LEVEL` | Pino log level (`fatal`, `error`, `warn`, `info`, `debug`, `trace`) | `string` | `info` | No |
| `DATABASE_URL` | PostgreSQL connection string | `string` | — | **Yes** (production) |
| `API_KEY` | Service-to-service API key for internal callers | `string` | — | **Yes** (production) |
| `JWT_SECRET` | Secret used to sign and verify JWT access tokens | `string` | — | **Yes** (production) |
| `CDN_BASE_URL` | Base URL for theme asset CDN (CloudFront / Cloudflare R2) | `string` | — | No |
| `CDN_SIGNING_KEY` | Private key for generating signed CDN download URLs | `string` | — | No |
| `STORAGE_BUCKET` | S3-compatible bucket name for theme asset uploads | `string` | — | **Yes** (production) |
| `STORAGE_REGION` | Cloud storage region | `string` | `us-east-1` | No |
| `STORAGE_ACCESS_KEY` | Cloud storage access key ID | `string` | — | **Yes** (production) |
| `STORAGE_SECRET_KEY` | Cloud storage secret access key | `string` | — | **Yes** (production) |
| `BILLING_API_URL` | Internal Billing API base URL for purchase verification | `string` | `http://localhost:3001` | No |
| `BILLING_API_KEY` | API key for authenticating with the Billing API | `string` | — | **Yes** (production) |
| `CORS_ORIGIN` | Allowed CORS origin(s), comma-separated | `string` | `*` | No |
| `RATE_LIMIT_MAX` | Max requests per rate-limit window | `number` | `100` | No |
| `RATE_LIMIT_WINDOW_MS` | Rate-limit window duration in milliseconds | `number` | `60000` | No |

## Authentication

All non-public endpoints require authentication. The API supports two authentication methods:

### JWT Bearer Tokens (Game Clients & Admin Apps)

Game clients and admin apps authenticate by sending a JWT in the `Authorization` header:

```
Authorization: Bearer <token>
```

- Tokens are issued by the auth service upon successful login
- Tokens contain the user's `sub` (user ID) and `roles` array
- Tokens expire after a configurable TTL (default: 1 hour)
- Refresh tokens are used to obtain new access tokens without re-authentication

### API Keys (Service-to-Service)

Internal services authenticate with a static API key in the `X-API-Key` header:

```
X-API-Key: <key>
```

- Used by game clients, admin apps, and other portfolio APIs for server-to-server calls
- Keys are configured via the `API_KEY` environment variable
- API key requests bypass user-scoped authorization checks

### Public Endpoints

The following endpoints do not require authentication:

| Endpoint | Purpose |
| -------- | ------- |
| `GET /health` | Health check |
| `GET /ready` | Readiness probe |
| `GET /docs` | Swagger UI |
| `GET /docs/json` | OpenAPI spec |
| `GET /catalog` | Public theme catalog browsing |

## Error Handling

All error responses follow a consistent JSON structure:

```json
{
  "statusCode": 404,
  "error": "Not Found",
  "message": "Theme pack 'enchanted-forest-v2' does not exist",
  "code": "THEME_NOT_FOUND"
}
```

| Field | Type | Description |
| ----- | ---- | ----------- |
| `statusCode` | `number` | HTTP status code |
| `error` | `string` | HTTP status text |
| `message` | `string` | Human-readable error description |
| `code` | `string?` | Machine-readable domain error code (optional) |

### HTTP Status Codes

| Status | Meaning | Example |
| ------ | ------- | ------- |
| `400` | Bad Request | Malformed JSON body or invalid file upload |
| `401` | Unauthorized | Missing or expired JWT / invalid API key |
| `403` | Forbidden | Valid auth but insufficient role (e.g., non-admin uploading themes) |
| `404` | Not Found | Theme pack or catalog item not found |
| `409` | Conflict | Theme with the same slug already exists |
| `413` | Payload Too Large | Theme asset upload exceeds size limit |
| `415` | Unsupported Media Type | Uploaded file is not a supported asset format |
| `422` | Unprocessable Entity | Business rule violation (e.g., incompatible game version) |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Unexpected failure |

### Domain Error Codes

| Code | Description |
| ---- | ----------- |
| `THEME_NOT_FOUND` | No theme pack exists with the given ID or slug |
| `THEME_ALREADY_EXISTS` | A theme with this slug is already in the catalog |
| `THEME_NOT_PUBLISHED` | Theme exists but is not in a published state |
| `ASSET_UPLOAD_FAILED` | Asset could not be stored (storage service error) |
| `ASSET_FORMAT_UNSUPPORTED` | Uploaded file type is not accepted |
| `ASSET_TOO_LARGE` | File exceeds the maximum allowed size |
| `DOWNLOAD_TOKEN_EXPIRED` | Signed download URL has expired |
| `DOWNLOAD_TOKEN_INVALID` | Download token signature could not be verified |
| `PURCHASE_NOT_VERIFIED` | Billing API could not confirm the user owns this theme |
| `GAME_INCOMPATIBLE` | Theme is not compatible with the requesting game version |
| `REVIEW_ALREADY_SUBMITTED` | User has already reviewed this theme |
| `CATEGORY_NOT_FOUND` | Category or tag does not exist |
| `WISHLIST_DUPLICATE` | Theme is already on the user's wishlist |

## Rate Limiting

Rate limiting is enforced via `@fastify/rate-limit` to protect against abuse:

| Scope | Limit | Window | Notes |
| ----- | ----- | ------ | ----- |
| **Global default** | 100 requests | 60 seconds | Per IP address |
| **Catalog browsing** | 200 requests | 60 seconds | Higher limit for public browsing |
| **Asset downloads** | 30 requests | 60 seconds | Prevents download abuse |
| **Asset uploads** | 10 requests | 60 seconds | Admin-only, upload-heavy |
| **Search endpoints** | 60 requests | 60 seconds | Prevents search abuse |
| **Health / readiness** | Unlimited | — | Excluded from rate limiting |

Rate-limited responses include standard headers:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 47
X-RateLimit-Reset: 1719000060
Retry-After: 12
```

When the limit is exceeded, the API returns `429 Too Many Requests` with the error body above.

## Data Models

Planned domain entities for the theme system. These will be implemented as Zod schemas in `src/domain/types.ts`:

### ThemePack

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `string (uuid)` | Unique theme pack identifier |
| `slug` | `string` | URL-friendly unique slug |
| `name` | `string` | Display name |
| `description` | `string` | Detailed description |
| `version` | `string (semver)` | Current version |
| `authorId` | `string (uuid)` | Creator's user ID |
| `status` | `'draft' \| 'review' \| 'published' \| 'archived'` | Publication state |
| `previewImageUrl` | `string` | URL to the preview thumbnail |
| `price` | `number` | Price in cents (`0` = free) |
| `currency` | `string` | ISO 4217 currency code |
| `tags` | `string[]` | Associated tag slugs |
| `categoryId` | `string (uuid)` | Primary category |
| `compatibleGames` | `string[]` | List of game identifiers this theme supports |
| `downloadCount` | `number` | Total downloads |
| `averageRating` | `number` | Aggregated star rating (1–5) |
| `createdAt` | `string (ISO 8601)` | Creation timestamp |
| `updatedAt` | `string (ISO 8601)` | Last update timestamp |

### AssetManifest

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `string (uuid)` | Manifest identifier |
| `themeId` | `string (uuid)` | Parent theme pack |
| `version` | `string (semver)` | Manifest version |
| `files` | `AssetFile[]` | Array of asset file entries |
| `totalSizeBytes` | `number` | Total download size |
| `checksum` | `string` | SHA-256 hash of the bundle |

### AssetFile

| Field | Type | Description |
| ----- | ---- | ----------- |
| `path` | `string` | Relative file path within the theme |
| `mimeType` | `string` | MIME type (e.g., `image/webp`, `audio/ogg`) |
| `sizeBytes` | `number` | File size |
| `checksum` | `string` | SHA-256 hash for integrity verification |

### DownloadToken

| Field | Type | Description |
| ----- | ---- | ----------- |
| `token` | `string` | Signed download token |
| `themeId` | `string (uuid)` | Theme being downloaded |
| `userId` | `string (uuid)` | Authorized user |
| `expiresAt` | `string (ISO 8601)` | Token expiry (short-lived, ~15 minutes) |
| `downloadUrl` | `string` | Signed CDN URL |

### PurchaseRecord

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `string (uuid)` | Purchase identifier |
| `userId` | `string (uuid)` | Buyer's user ID |
| `themeId` | `string (uuid)` | Purchased theme |
| `pricePaid` | `number` | Amount paid in cents |
| `currency` | `string` | ISO 4217 currency code |
| `source` | `'billing_api' \| 'promo' \| 'bundle'` | How the purchase was made |
| `purchasedAt` | `string (ISO 8601)` | Purchase timestamp |

### Category

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `string (uuid)` | Category identifier |
| `slug` | `string` | URL-friendly name |
| `name` | `string` | Display name |
| `description` | `string` | Category description |
| `parentId` | `string (uuid)?` | Parent category for nesting |
| `sortOrder` | `number` | Display order |

### Review

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `string (uuid)` | Review identifier |
| `userId` | `string (uuid)` | Reviewer's user ID |
| `themeId` | `string (uuid)` | Reviewed theme |
| `rating` | `number` | Star rating (1–5) |
| `title` | `string?` | Optional review title |
| `body` | `string?` | Optional review text |
| `createdAt` | `string (ISO 8601)` | Submission timestamp |
| `updatedAt` | `string (ISO 8601)` | Last edit timestamp |

## Deployment

### Docker (Recommended)

```bash
# Build the production image
docker build -t themes-api .

# Run with environment variables
docker run -d \
  --name themes-api \
  -p 3000:3000 \
  -e NODE_ENV=production \
  -e DATABASE_URL=postgresql://user:pass@db:5432/themes \
  -e JWT_SECRET=your-jwt-secret \
  -e STORAGE_BUCKET=themes-assets \
  -e STORAGE_ACCESS_KEY=AKIA... \
  -e STORAGE_SECRET_KEY=... \
  -e BILLING_API_URL=http://billing-api:3000 \
  -e BILLING_API_KEY=internal-key \
  themes-api
```

### Health Checks

Configure your orchestrator (Docker Compose, Kubernetes, ECS) to use the built-in probes:

| Probe | Endpoint | Interval | Timeout | Failure Threshold |
| ----- | -------- | -------- | ------- | ----------------- |
| Liveness | `GET /health` | 30s | 5s | 3 |
| Readiness | `GET /ready` | 10s | 5s | 3 |

### Production Checklist

- [ ] Set `NODE_ENV=production`
- [ ] Provide all **required** environment variables (see [Environment Variables](#environment-variables))
- [ ] Configure database connection pooling (recommended: 10–20 connections)
- [ ] Enable TLS termination at the load balancer / reverse proxy
- [ ] Set up database migrations before first deploy
- [ ] Configure cloud storage bucket with appropriate CORS and lifecycle policies
- [ ] Set up CDN for theme asset delivery
- [ ] Configure log aggregation (Pino outputs structured JSON)
- [ ] Set up monitoring alerts on `/health` and `/ready`
- [ ] Enable CORS for specific origins (do not use `*` in production)
- [ ] Set asset upload size limits at the reverse proxy level

## Supported Clients

This API is consumed by the following applications:

| Client | Type | Description |
| ------ | ---- | ----------- |
| **[🎨 Theme Store](https://github.com/scottdreinhart/theme-store)** | Admin App | Theme catalog management, uploads, reviews moderation |
| **All portfolio games** | Game Clients | Browse catalog, download purchased themes, check compatibility |
| **[💳 Billing API](https://github.com/scottdreinhart/billing-api)** | API (internal) | Purchase fulfillment callbacks when a theme is bought |

## Versioning

The API uses **URL-prefix versioning**:

```
https://api.example.com/v1/catalog
https://api.example.com/v1/themes
```

- All current endpoints are under `/v1/`
- Breaking changes will be introduced under `/v2/` with a deprecation notice on `/v1/`
- Non-breaking additions (new fields, new endpoints) are added to the current version
- Deprecated versions will be supported for a minimum of **6 months** after the successor is released
- The OpenAPI spec at `/docs/json` includes the version in its `info.version` field

## Remaining Work

### Core Functionality

- [ ] **Domain model implementation** — define entities, value objects, and business rules for theme packs, catalog items, asset manifests, download tokens, purchase records, and install states
- [ ] **Database integration** — connect to PostgreSQL/SQLite via Drizzle ORM or Prisma
- [ ] **CRUD route scaffolding** — RESTful endpoints for all domain entities
- [ ] **Authentication** — JWT or API key verification middleware
- [ ] **Authorization** — role-based access control for admin vs. client operations

### Code Quality & Testing

- [ ] **Unit tests** — domain functions and use-case services
- [ ] **Integration tests** — route handlers with database fixtures
- [ ] **E2E tests** — full request lifecycle with Supertest or Fastify inject

### DevOps & Deployment

- [ ] **CI/CD pipeline** — GitHub Actions workflow for lint → test → build → deploy
- [ ] **Database migrations** — version-controlled schema changes
- [ ] **Environment configs** — staging, production, and preview environments
- [ ] **Monitoring** — health-check alerts, error tracking, APM integration

## Future Improvements

- [ ] **CDN integration** — serve theme assets from a global CDN (CloudFront / Cloudflare R2) with signed URLs for access control
- [ ] **Image optimization pipeline** — auto-generate preview thumbnails, WebP variants, and responsive sizes on theme upload
- [ ] **Theme preview sandbox** — API endpoint that renders a live preview of a theme applied to a sample game board
- [ ] **Incremental asset sync** — delta downloads so game clients only fetch changed files when a theme updates
- [ ] **User-generated themes** — allow players to upload custom themes with moderation queue and approval workflow
- [ ] **Theme ratings & reviews** — endpoints for users to rate/review themes with aggregated scores on catalog listings
- [ ] **Seasonal collections** — auto-curate time-limited theme bundles (holiday, event-based) with scheduled publish/unpublish
- [ ] **Cross-game compatibility matrix** — track which themes work with which games and auto-filter the catalog per game
- [ ] **Analytics endpoints** — report most-downloaded, highest-rated, and trending themes for storefront personalization
- [ ] **Webhook notifications** — notify game clients when a purchased theme receives an update

## Portfolio Services

Infrastructure services supporting the game portfolio:

| Service | Description |
| ------- | ----------- |
| **[💳 Game Billing](https://github.com/scottdreinhart/game-billing)** | Payment processing & subscription management (Admin App) |
| **[🎨 Theme Store](https://github.com/scottdreinhart/theme-store)** | DLC theme downloader & manager (Admin App) |
| **[📺 Ad Network](https://github.com/scottdreinhart/ad-network)** | Ad serving & revenue management (Admin App) |
| **[💳 Billing API](https://github.com/scottdreinhart/billing-api)** | Fastify API backend for billing |
| **[📺 Ads API](https://github.com/scottdreinhart/ads-api)** | Fastify API backend for ads |
| **[🏆 Rankings API](https://github.com/scottdreinhart/rankings-api)** | Fastify API backend for rankings & leaderboards |

## Portfolio Games

All games in this portfolio share the same React + Vite + TypeScript + CLEAN architecture stack:

| Game | Description | Complexity |
| ---- | ----------- | ---------- |
| **[Tic-Tac-Toe](https://github.com/scottdreinhart/tictactoe)** | Classic 3×3 grid game with 4 AI difficulty levels and series mode | Baseline — the reference architecture |
| **[Shut the Box](https://github.com/scottdreinhart/shut-the-box)** | Roll dice, flip numbered tiles to match the total; lowest remaining sum wins | Similar — grid UI + dice logic |
| **[Mancala (Kalah)](https://github.com/scottdreinhart/mancala)** | Two-row pit-and-stones capture game; simple rules, satisfying chain moves | Slightly higher — seed-sowing animation |
| **[Connect Four](https://github.com/scottdreinhart/connect-four)** | Drop discs into a 7×6 grid; first to four in a row wins | Similar — larger grid, same win-check pattern |
| **[Simon Says](https://github.com/scottdreinhart/simon-says)** | Repeat a growing sequence of colors/sounds; memory challenge | Similar — leverages existing Web Audio API |
| **[Lights Out](https://github.com/scottdreinhart/lights-out)** | Toggle a 5×5 grid of lights; goal is to turn them all off | Similar — grid + toggle logic |
| **[Nim](https://github.com/scottdreinhart/nim)** | Players take turns removing objects from piles; last to take loses | Simpler — minimal UI, pure strategy |
| **[Hangman](https://github.com/scottdreinhart/hangman)** | Guess letters to reveal a hidden word before the stick figure completes | Similar — alphabet grid + SVG drawing |
| **[Memory / Concentration](https://github.com/scottdreinhart/memory-game)** | Flip cards to find matching pairs on a grid | Similar — grid + flip animation |
| **[2048](https://github.com/scottdreinhart/2048)** | Slide numbered tiles on a 4×4 grid; merge matching tiles to reach 2048 | Slightly higher — swipe input + merge logic |
| **[Reversi (Othello)](https://github.com/scottdreinhart/reversi)** | Place discs to flip opponent's pieces; most discs wins | Moderately higher — flip-chain logic + AI |
| **[Checkers](https://github.com/scottdreinhart/checkers)** | Classic diagonal-move capture board game | Higher — move validation + multi-jump |
| **[Battleship](https://github.com/scottdreinhart/battleship)** | Place ships on a grid, take turns guessing opponent locations | Moderately higher — two-board UI + ship placement |
| **[Snake](https://github.com/scottdreinhart/snake)** | Steer a growing snake to eat food without hitting walls or itself | Different — real-time game loop instead of turn-based |
| **[Monchola](https://github.com/scottdreinhart/monchola)** | Traditional dice/board race game with capture mechanics | Similar — dice roll + board path + capture rules |
| **[Rock Paper Scissors](https://github.com/scottdreinhart/rock-paper-scissors)** | Best-of-N rounds against the CPU with hand animations | Simpler — minimal state, animation-focused |
| **[Minesweeper](https://github.com/scottdreinhart/minesweeper)** | Reveal cells on a minefield grid without detonating hidden mines | Moderately higher — flood-fill reveal + flag logic |

## Contributing

This is proprietary software. Contributions are accepted by invitation only.

If you have been granted contributor access:

1. Create a feature branch from `main`
2. Make focused, single-purpose commits with clear messages
3. Run `pnpm validate` before pushing (lint + format + build gate)
4. Submit a pull request with a description of the change

See the [LICENSE](LICENSE) file for usage restrictions.

## License

Copyright © 2026 Scott Reinhart. All Rights Reserved.

This project is proprietary software. No permission is granted to use, copy, modify, or distribute this software without the prior written consent of the owner. See the [LICENSE](LICENSE) file for full terms.

---
[⬆ Back to top](#-themes-api)
