# Shopify App Starter (Remix)

![Shopify Plus Partner](https://img.shields.io/badge/Shopify-Plus%20Partner-green)
![License](https://img.shields.io/badge/License-MIT-blue.svg)
![React](https://img.shields.io/badge/React-18.2.0-blue.svg)
![Remix](https://img.shields.io/badge/Remix-v2.15.0-blue)
![Vite](https://img.shields.io/badge/Vite-5.1.3-blue.svg)
![TypeScript](https://img.shields.io/badge/Typescript-5.2.2-blue.svg)
![Prisma](https://img.shields.io/badge/Prisma-6.2.1-blue.svg)

A modern, production-ready template for building Shopify apps using the [Remix](https://remix.run) framework. This template provides everything you need to start building high-quality Shopify apps with a focus on developer experience and best practices.

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Authentication and Data Querying](#authentication-and-data-querying)
- [Webhooks](#webhooks)
- [App Extensions](#app-extensions)
- [Deployment](#deployment)
- [Database Options](#database-options)
- [Customization](#customization)
- [Performance Considerations](#performance-considerations)
- [Security Best Practices](#security-best-practices)
- [Internationalization](#internationalization)
- [Troubleshooting](#troubleshooting)
- [Tech Stack](#tech-stack)
- [Resources](#resources)
- [Changelog](#changelog)
- [Contributing](#contributing)
- [License](#license)

## Features

- 🔐 **Built-in OAuth** - Seamless Shopify app installation and permission handling
- 🔄 **GraphQL & REST Support** - Easy access to Shopify's Admin API
- 🔔 **Webhook Management** - Built-in webhook processing and verification
- 🧩 **App Bridge Integration** - Leverage Shopify's App Bridge for native-like experiences
- 🎨 **Polaris Design System** - Build UIs that match the Shopify admin experience
- 🔒 **Session Storage** - Secure session management with Prisma
- 📱 **Responsive Design** - Works on all devices and screen sizes
- 🌐 **Embedded App Support** - Runs seamlessly within the Shopify admin
- 🚀 **Vite-Powered Development** - Fast refresh and build times
- 📦 **Zero Config Deployment** - Ready for deployment to various platforms
- 🛠️ **TypeScript Support** - Full type safety across your codebase

## Prerequisites

Before you begin, make sure you have:

- **Node.js** - Version ^18.20 or higher ([Download](https://nodejs.org/en/download/))
- **Shopify Partner Account** - [Create one](https://partners.shopify.com/signup) if you don't have it
- **Shopify Development Store** - For testing ([Create a development store](https://help.shopify.com/en/partners/dashboard/development-stores#create-a-development-store))
- **Shopify CLI** - Will be installed with the project dependencies
- **Chrome** or **Firefox** - Latest version recommended for development
- **Git** - For version control and deployment
- **A package manager** - npm, yarn, or pnpm

## Quick Start

The fastest way to get started is using the Shopify CLI:

```bash
# Create a new app using the Shopify CLI
npm create @shopify/app@latest
```

Or if you're cloning this repository directly:

```bash
# Clone the repository
git clone https://github.com/RMNCLDYO/shopify-app-starter.git your-app-name

# Navigate to the project directory
cd your-app-name

# Install dependencies
npm install

# Start the development server
npm run dev
```

## Getting Started

### Creating a New App

The recommended way to start is using the Shopify CLI:

```bash
npm create @shopify/app@latest
# or
yarn create @shopify/app
# or
pnpm create @shopify/app
```

Then select "Remix" as your framework.

### Manual Installation

If you've cloned this repo directly:

```bash
# Using npm
npm install

# Using yarn
yarn install

# Using pnpm
pnpm install
```

### Local Development

Start your development server:

```bash
# Using npm
npm run dev

# Using yarn
yarn dev

# Using pnpm
pnpm run dev
```

This will:
1. Start the Remix development server
2. Connect to your Shopify Partner account
3. Set up necessary environment variables
4. Create a tunnel to your local machine
5. Provide a URL to install your app on a development store

Press 'p' in the terminal to open the install URL once the server is running.

## Project Structure

Key files and directories:

```
├── app/
│   ├── routes/                # Application routes
│   │   ├── app._index.tsx     # Main app page
│   │   ├── app.additional.tsx # Additional page example
│   │   ├── webhooks.*.tsx     # Webhook handlers
│   ├── shopify.server.ts      # Shopify app configuration
│   ├── db.server.ts           # Database client setup
├── prisma/                    # Prisma database schema and migrations
│   ├── schema.prisma         # Database schema definition
│   ├── migrations/          # Database migrations
├── public/                    # Static assets
├── extensions/                # Shopify app extensions
├── shopify.app.toml          # Shopify app configuration
├── shopify.web.toml          # Web framework configuration
├── package.json             # Project dependencies and scripts
├── tsconfig.json           # TypeScript configuration
```

### Core Files

- **app/shopify.server.ts**: Configures your Shopify app with API keys, scopes, and other settings
- **app/db.server.ts**: Sets up Prisma client and database connections
- **app/routes/app._index.tsx**: Main app page with examples of querying the Shopify API
- **app/routes/app.tsx**: App layout and navigation using App Bridge and Polaris
- **app/routes/auth.$.tsx**: Handles OAuth flow and authentication
- **app/routes/webhooks.*.tsx**: Webhook handlers for app lifecycle events

## Authentication and Data Querying

This template simplifies Shopify authentication and data access using the `shopify` object exported from `/app/shopify.server.ts`:

```javascript
export async function loader({ request }) {
  const { admin } = await shopify.authenticate.admin(request);

  // Query products using GraphQL
  const response = await admin.graphql(`
    {
      products(first: 10) {
        nodes {
          id
          title
        }
      }
    }`);

  const { data } = await response.json();
  return data.products.nodes;
}
```

### GraphQL vs REST

The template encourages using GraphQL for accessing the Shopify Admin API. GraphQL provides several advantages:

- Request only the data you need
- Combine multiple resources in a single request
- Strong typing and documentation
- Better performance for complex data requirements

REST API access is also available but GraphQL is the recommended approach for most use cases.

## Webhooks

The template includes handlers for required Shopify webhooks:

- **App Uninstalled** - Cleans up when a merchant uninstalls your app
- **Scopes Update** - Handles changes to your app's permission scopes

To configure webhooks, edit the `shopify.app.toml` file:

```toml
[[webhooks.subscriptions]]
topics = [ "app/uninstalled" ]
uri = "/webhooks/app/uninstalled"

[[webhooks.subscriptions]]
topics = [ "app/scopes_update" ]
uri = "/webhooks/app/scopes_update"
```

For shop-specific webhooks that can't be defined in the TOML file, use the `shopify.registerWebhooks` method in your app code.

### Adding Custom Webhooks

To add custom webhooks, create new webhook handlers in the `app/routes/webhooks.{topic}.tsx` pattern. For example:

```typescript
// app/routes/webhooks.products.create.tsx
import { authenticate } from "../shopify.server";
import { ActionFunctionArgs } from "@remix-run/node";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { payload, topic } = await authenticate.webhook(request);
  
  // Process webhook payload
  console.log(`Received ${topic} webhook with payload:`, payload);
  
  return new Response(null, { status: 200 });
};
```

Then register the webhook in your `shopify.app.toml` file:

```toml
[[webhooks.subscriptions]]
topics = [ "products/create" ]
uri = "/webhooks/products/create"
```

## App Extensions

This template supports Shopify app extensions, which allow you to extend the functionality of Shopify.

To create an extension:

```bash
npm run shopify app generate extension
```

Follow the prompts to select the extension type and configure it. Extensions are stored in the `extensions/` directory.

See the [Shopify Extensions documentation](https://shopify.dev/docs/apps/app-extensions) for more details on available extension types and development workflows.

## Deployment

When you're ready to deploy your app to production:

1. **Build your app**:
   ```bash
   npm run build
   ```

2. **Choose a hosting platform**:
   - [Heroku](https://www.heroku.com/)
   - [Fly.io](https://fly.io/)
   - [Vercel](https://vercel.com/)
   - [Digital Ocean App Platform](https://www.digitalocean.com/products/app-platform)
   - Any other Node.js hosting platform

3. **Set environment variables**:
   - `SHOPIFY_API_KEY` - Your Shopify API key
   - `SHOPIFY_API_SECRET` - Your Shopify API secret
   - `SCOPES` - Required app scopes (comma-separated)
   - `HOST` - Your app's host URL
   - `NODE_ENV=production` - Set environment to production
   - `DATABASE_URL` - Your database connection string (if not using SQLite)
   - `ENCRYPTION_STRING` - A random string for session encryption

4. **Deploy your database** (if not using SQLite)

### Docker Deployment

This template includes a Dockerfile for containerized deployment. To build and run the Docker image:

```bash
# Build the Docker image
docker build -t shopify-app-remix .

# Run the Docker container
docker run -p 8081:8081 \
  -e SHOPIFY_API_KEY=your_api_key \
  -e SHOPIFY_API_SECRET=your_api_secret \
  -e HOST=https://your-app-url.com \
  -e SCOPES=write_products,read_customers \
  -e ENCRYPTION_STRING=random_string \
  shopify-app-remix
```

See the [Shopify app deployment documentation](https://shopify.dev/docs/apps/deployment/web) for more details.

## Database Options

This template uses SQLite with Prisma by default, which works for development and single-instance production deployments. For multi-instance deployments, consider these options:

| Database   | Type             | Recommended Providers                                                   |
|------------|------------------|-------------------------------------------------------------------------|
| MySQL      | SQL              | Digital Ocean, Planet Scale, Amazon Aurora, Google Cloud SQL            |
| PostgreSQL | SQL              | Digital Ocean, Amazon Aurora, Google Cloud SQL                          |
| MongoDB    | NoSQL / Document | Digital Ocean, MongoDB Atlas                                            |

To configure MongoDB with Prisma, you'll need to modify your schema.prisma:

```prisma
model Session {
  session_id  String    @id @default(auto()) @map("_id") @db.ObjectId
  id          String    @unique
  // ... other fields
}
```

See the [Prisma documentation](https://www.prisma.io/docs/orm/overview/databases/mongodb) for more details.

### Database Migrations

This template uses Prisma Migrate to manage database schemas and migrations:

```bash
# Generate a new migration
npx prisma migrate dev --name add_new_field

# Apply migrations in production
npx prisma migrate deploy
```

## Customization

### Non-Embedded Apps

By default, this template is configured for embedded apps. To create a non-embedded app:

1. Change the `isEmbeddedApp` prop to `false` in `/app/routes/app.tsx`
2. Remove any App Bridge API usage (window.shopify)
3. Update the shopify config in `app/shopify.server.ts` with `isEmbeddedApp: false`
4. Set `embedded = false` in your `shopify.app.toml` file

### Updating Scopes

If you change your app's scopes, run:

```bash
npm run deploy
```

This updates your scopes with Shopify and prevents OAuth loops.

### Styling with Polaris

This template uses [Polaris](https://polaris.shopify.com/), Shopify's design system, for UI components. Polaris helps create interfaces that match the Shopify admin experience.

```tsx
import { Page, Card, Layout, Text, BlockStack } from "@shopify/polaris";

export default function MyPage() {
  return (
    <Page title="My Page">
      <Layout>
        <Layout.Section>
          <Card>
            <BlockStack gap="400">
              <Text variant="headingMd">Hello Shopify!</Text>
              <Text>Build amazing app experiences with Polaris components.</Text>
            </BlockStack>
          </Card>
        </Layout.Section>
      </Layout>
    </Page>
  );
}
```

## Performance Considerations

### Optimization Techniques

- Use the `defer` API from Remix for non-critical data loading
- Break large routes into smaller components
- Use Shopify's GraphQL API efficiently by requesting only needed fields
- Implement proper caching strategies for API responses
- Optimize image assets and leverage modern formats

### Monitoring

Consider implementing monitoring solutions:

- [Sentry](https://sentry.io/) for error tracking
- [LogRocket](https://logrocket.com/) for session replay and debugging
- [Datadog](https://www.datadoghq.com/) for performance monitoring

## Security Best Practices

- Never expose your `SHOPIFY_API_SECRET` or other sensitive environment variables
- Store API keys and secrets securely in environment variables
- Always verify webhook signatures using the built-in authentication
- Implement proper CORS policies for non-embedded apps
- Use HTTPS for all communication, even in development
- Follow the principle of least privilege when requesting API scopes
- Regularly update dependencies to patch security vulnerabilities

## Internationalization

To internationalize your app:

1. Install [react-intl](https://formatjs.io/docs/react-intl/) or similar i18n library
2. Create translation files for supported languages
3. Use translation functions instead of hardcoded strings
4. Detect merchant locale from Shopify's Admin API
5. See [Shopify's internationalization guide](https://shopify.dev/docs/apps/best-practices/internationalization/getting-started) for best practices

## Troubleshooting

### Database tables don't exist

If you get a "The table `main.Session` does not exist" error, run the setup script:

```bash
npm run setup
```

### Navigating/redirecting breaks an embedded app

For embedded apps, follow these rules:

1. Use `<Link>` from `@remix-run/react` or `@shopify/polaris` (not `<a>`)
2. Use the `redirect` helper from `authenticate.admin` (not from `@remix-run/node`)
3. Use `useSubmit` or `<Form/>` from `@remix-run/react` (not lowercase `<form/>`)

### Webhook validation failures

Admin-created webhook subscriptions will fail HMAC validation. Instead:

1. Use app-specific webhooks defined in your TOML file (recommended)
2. Create webhook subscriptions using the `shopifyApp` object

### Testing with Streaming Responses

To test streaming responses (defer/await) locally:

1. Set up ngrok: https://ngrok.com/product/secure-tunnels
2. Create a tunnel: `ngrok http 8080`
3. Run the CLI with your tunnel URL: `yarn shopify app dev --tunnel-url=YOUR_TUNNEL_URL:8080`

### Clock Sync Issue

If you get a "nbf claim timestamp check failed" error, ensure your computer's clock is synchronized with internet time servers.

### Rate Limiting

If you encounter API rate limiting:

1. Implement exponential backoff retries for API calls
2. Use bulk operations for large datasets
3. Cache responses when appropriate
4. Use GraphQL to minimize the number of API requests

## Tech Stack

This template uses:

- [**Remix**](https://remix.run) - Full-stack web framework
- [**Shopify App Remix**](https://shopify.dev/docs/api/shopify-app-remix) - Authentication and Shopify API tools
- [**Shopify App Bridge**](https://shopify.dev/docs/apps/tools/app-bridge) - Seamless app integration with Shopify Admin
- [**Polaris**](https://polaris.shopify.com) - Shopify's design system and component library
- [**Prisma**](https://www.prisma.io) - Type-safe database ORM
- [**GraphQL**](https://graphql.org) - API query language
- [**TypeScript**](https://www.typescriptlang.org/) - Type-safe JavaScript
- [**Vite**](https://vitejs.dev/) - Next generation frontend tooling

## Resources

- [Remix Documentation](https://remix.run/docs/en/v1)
- [Shopify App Remix Documentation](https://shopify.dev/docs/api/shopify-app-remix)
- [Introduction to Shopify Apps](https://shopify.dev/docs/apps/getting-started)
- [Shopify CLI Documentation](https://shopify.dev/docs/apps/tools/cli)
- [Shopify App Extensions](https://shopify.dev/docs/apps/app-extensions/list)
- [Shopify Functions](https://shopify.dev/docs/api/functions)
- [App Internationalization](https://shopify.dev/docs/apps/best-practices/internationalization/getting-started)
- [Shopify GraphQL Admin API](https://shopify.dev/docs/api/admin-graphql)
- [Shopify Partner Dashboard](https://partners.shopify.com)
- [Shopify Community Forums](https://community.shopify.com/)
- [Shopify Developer Documentation](https://shopify.dev/docs)
- [Polaris Components](https://polaris.shopify.com/components)
- [Remix Discord](https://discord.com/invite/remix)

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and updates.

## Contributing

We welcome contributions to improve this template! Here's how you can help:

1. **Report Issues**: Found a bug or have a feature request? [Open an issue](https://github.com/RMNCLDYO/shopify-app-starter/issues/new).
2. **Fix Issues**: Check our [open issues](https://github.com/RMNCLDYO/shopify-app-starter/issues) to find something to work on.
3. **Submit PRs**: Submit pull requests with bug fixes or enhancements.

Please follow our [contribution guidelines](https://github.com/RMNCLDYO/shopify-app-starter/blob/main/.github/CONTRIBUTING.md) when submitting code.

## License

This template is available under the [MIT](./LICENSE) license.