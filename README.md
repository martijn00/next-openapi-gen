# next-openapi-gen

`next-openapi-gen` generates an OpenAPI 3.0 spec from Next.js route files, JSDoc annotations, and shared schema definitions. It supports App Router and Pages Router projects, works with Zod or plain TypeScript types, and can scaffold a docs page for several UI providers.

## What it supports

- Next.js App Router and Pages Router API routes
- Zod schemas, TypeScript types, mixed schema directories, and custom YAML or JSON schemas
- Scalar, Swagger UI, Redoc, Stoplight Elements, RapiDoc, or no UI page at all
- Route-level metadata from JSDoc tags such as `@response`, `@body`, `@auth`, and `@operationId`
- Generated error responses through `defaultResponseSet`, `responseSets`, and `errorConfig`

## Installation

Choose the package manager that matches your app:

```bash
npm install --save-dev next-openapi-gen
```

```bash
pnpm add --save-dev next-openapi-gen
```

```bash
yarn add --dev next-openapi-gen
```

## Quick start

Initialize the config and optional docs page:

```bash
npx next-openapi-gen init
```

Generate the OpenAPI document:

```bash
npx next-openapi-gen generate
```

By default this creates:

- `next.openapi.json`
- `public/openapi.json`
- `src/app/api-docs/page.tsx` or `app/api-docs/page.tsx` when a UI is enabled

The `init` command auto-detects your app's package manager from lockfiles and installs the schema or UI dependencies it needs.

## CLI reference

### `init`

```bash
npx next-openapi-gen init [options]
```

| Option | Choices | Default | Description |
| --- | --- | --- | --- |
| `--ui <type>` | `scalar`, `swagger`, `redoc`, `stoplight`, `rapidoc`, `none` | `scalar` | UI provider to scaffold |
| `--schema <type>` | `zod`, `typescript` | `zod` | Default schema strategy |
| `--docs-url <path>` | any string | `api-docs` | Docs URL value to store in the generated config |
| `--output <file>` | any path | `next.openapi.json` | Path for the config template |

Use `--ui none` when you only want the OpenAPI spec and will host the docs elsewhere.

### `generate`

```bash
npx next-openapi-gen generate --template next.openapi.json
```

| Option | Default | Description |
| --- | --- | --- |
| `--template <file>` | `next.openapi.json` | OpenAPI config file to load |

## Configuration

`next.openapi.json` controls where routes and schemas are discovered and how the output is written.

```json
{
  "openapi": "3.0.0",
  "info": {
    "title": "API Documentation",
    "version": "1.0.0",
    "description": "This is the OpenAPI specification for your project."
  },
  "servers": [
    {
      "url": "http://localhost:3000/api",
      "description": "Local development server"
    }
  ],
  "components": {
    "securitySchemes": {
      "BearerAuth": {
        "type": "http",
        "scheme": "bearer",
        "bearerFormat": "JWT"
      }
    }
  },
  "defaultResponseSet": "common",
  "responseSets": {
    "common": ["400", "500"],
    "auth": ["400", "401", "403", "500"],
    "public": ["400", "500"]
  },
  "errorConfig": {
    "template": {
      "type": "object",
      "properties": {
        "error": {
          "type": "string",
          "example": "{{ERROR_MESSAGE}}"
        },
        "code": {
          "type": "string",
          "example": "{{ERROR_CODE}}"
        }
      }
    },
    "codes": {
      "400": {
        "description": "Bad Request",
        "variables": {
          "ERROR_MESSAGE": "Invalid request parameters",
          "ERROR_CODE": "BAD_REQUEST"
        }
      },
      "401": {
        "description": "Unauthorized",
        "variables": {
          "ERROR_MESSAGE": "Authentication required",
          "ERROR_CODE": "UNAUTHORIZED"
        }
      }
    }
  },
  "apiDir": "./src/app/api",
  "schemaDir": "./src",
  "schemaType": "zod",
  "schemaFiles": [],
  "docsUrl": "api-docs",
  "ui": "scalar",
  "outputFile": "openapi.json",
  "outputDir": "./public",
  "includeOpenApiRoutes": false,
  "ignoreRoutes": [],
  "debug": false
}
```

### Config fields

| Field | Description |
| --- | --- |
| `apiDir` | API route directory, for example `./src/app/api` or `./pages/api` |
| `routerType` | Optional router hint: `app` or `pages` |
| `schemaDir` | A directory or list of directories that contain referenced schemas |
| `schemaType` | `zod`, `typescript`, or an array containing both |
| `schemaFiles` | Extra OpenAPI YAML or JSON schema files to merge in |
| `docsUrl` | Docs URL value stored in the generated config |
| `ui` | Selected UI provider |
| `outputFile` | Generated file name, usually `openapi.json` |
| `outputDir` | Directory where the spec file is written |
| `includeOpenApiRoutes` | Restrict generation to routes with an explicit `@openapi` tag |
| `ignoreRoutes` | Wildcard patterns for routes that should be excluded |
| `defaultResponseSet` | Named response set automatically added to endpoints |
| `responseSets` | Map of reusable HTTP response codes |
| `errorConfig` | Template-based error schema generator |
| `debug` | Enable extra generator logging |

## Route documentation

### Zod example

```typescript
import { z } from "zod";

export const ProductParams = z.object({
  id: z.string().describe("Product ID"),
});

export const ProductResponse = z.object({
  id: z.string(),
  name: z.string(),
  price: z.number().positive(),
});

/**
 * Get product information
 * @description Fetch a product by ID
 * @pathParams ProductParams
 * @response ProductResponse
 * @openapi
 */
export async function GET() {}
```

### TypeScript example

```typescript
type UserParams = {
  id: string;
};

type UserResponse = {
  id: string;
  email: string;
  name: string;
};

/**
 * Get user information
 * @pathParams UserParams
 * @response UserResponse
 * @openapi
 */
export async function GET() {}
```

### Pages Router example

Pages Router handlers require `@method` because the HTTP method cannot be inferred from the export name:

```typescript
/**
 * List users
 * @response UserResponse[]
 * @method GET
 * @openapi
 */
/**
 * Create a user
 * @body CreateUserBody
 * @response 201:UserResponse
 * @method POST
 * @openapi
 */
export default function handler() {}
```

## Supported JSDoc tags

| Tag | Description |
| --- | --- |
| `@description` | Endpoint description |
| `@operationId` | Override the generated operation ID |
| `@pathParams` | Path parameter schema or type |
| `@params` | Query parameter schema or type |
| `@queryParams` | Alias for `@params` |
| `@body` | Request body schema or type |
| `@bodyDescription` | Request body description |
| `@response` | Response schema or type, optionally with code and description |
| `@responseDescription` | Fallback response description |
| `@responseSet` | Named response set override |
| `@add` | Extra response codes to add |
| `@contentType` | Request content type, such as `multipart/form-data` |
| `@auth` | Security requirement, including comma-separated multiple schemes |
| `@tag` | Custom OpenAPI tag |
| `@deprecated` | Mark an operation as deprecated |
| `@openapi` | Required when `includeOpenApiRoutes` is `true` |
| `@ignore` | Exclude a route from the generated spec |
| `@method` | Required for Pages Router route handlers |

## Supported schema patterns

- Zod primitives, objects, enums, chained validators, `z.infer`, and `.extend()`
- TypeScript utility types such as `Awaited`, `ReturnType`, and `Parameters`
- Generic type wrappers and nested references
- `drizzle-zod` generated schemas
- Mixed Zod and TypeScript projects with optional custom YAML or JSON schema files

Resolution order is:

1. `schemaFiles`
2. Zod schemas
3. TypeScript types

## Examples

The `examples/` directory shows the main supported workflows:

- `examples/next15-app-zod`
- `examples/next15-app-typescript`
- `examples/next15-pages-router`
- `examples/next15-app-mixed-schemas`
- `examples/next15-app-drizzle-zod`
- `examples/next15-app-scalar`
- `examples/next15-app-swagger`

To run one locally:

```bash
cd examples/next15-app-zod
npm install
npx next-openapi-gen generate
npm run dev
```

Then open `http://localhost:3000/api-docs`.

## Development

Local development for this package uses npm:

```bash
npm install
npm test
npm run build
npx tsc --noEmit
```

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for contributor workflow details.

## Changelog

Release history lives in [`CHANGELOG.md`](./CHANGELOG.md).

## License

Released under the [MIT License](./LICENSE).
