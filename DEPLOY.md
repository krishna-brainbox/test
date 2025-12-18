# Deployment Guide

This guide provides steps to deploy your Shopify app to a hosting provider (e.g., Fly.io, Render, Heroku).

## 1. Prerequisites

- A hosting provider account.
- A production database (PostgreSQL recommended).
- `shopify.app.production.toml` configured with your production URLs.

## 2. Environment Variables

Set the following environment variables in your hosting provider's dashboard:

| Variable | Description |
| :--- | :--- |
| `SHOPIFY_API_KEY` | Client ID from `shopify app env show` |
| `SHOPIFY_API_SECRET` | Client Secret from `shopify app env show` |
| `SCOPES` | `write_products,read_products,write_discounts,read_discounts` |
| `SHOPIFY_APP_URL` | Your production app URL (e.g., `https://myapp.fly.dev`) |
| `DATABASE_URL` | Connection string for your production PostgreSQL database |
| `NODE_ENV` | Set to `production` |
| `PORT` | Set to `3000` (or whatever port your provider expects) |

## 3. Deployment Steps

### Step 1: Push Code
Push your code to your git repository connected to your hosting provider.

### Step 2: Build Command
Your hosting provider should automatically run the instructions in your `Dockerfile`. 
If you need to specify a build command manually:
```bash
npm run build
```

### Step 3: Database Migration
**IMPORTANT:** After deployment, or as part of the release phase, you MUST run migrations to set up your production database tables.
```bash
npm run setup
```
*Note: `npm run setup` runs `prisma generate && prisma migrate deploy`.*

### Step 4: Update Shopify Configuration
Once deployed, update Shopify's records with your new URLs:

```bash
# Verify the production config has the correct URLs
cat shopify.app.production.toml

# Push config to Shopify
path/to/cli/shopify app deploy --config=shopify.app.production.toml
```

## 4. Troubleshooting

- **Database Errors**: Ensure `DATABASE_URL` is correct and accessible from your hosting provider.
- **Build Fails**: Check logs. If `prisma` is missing, ensure `npm install` ran successfully.
