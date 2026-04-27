# Furniture Assembly Full-Stack Starter

Full-stack template inspired by modern service-business sites, built with:

- `frontend`: Next.js 16 App Router + Tailwind CSS
- `backend`: Strapi 5 (headless CMS, SQLite for local development)

## Project structure

```txt
.
├─ frontend/
│  ├─ src/app/                # App Router pages, API route, sitemap/robots
│  ├─ src/components/         # Reusable UI building blocks
│  ├─ src/lib/                # Strapi client + SEO helpers
│  └─ src/types/              # Shared content models
└─ backend/
   ├─ src/api/                # Strapi content types (services/pages/blog posts)
   ├─ src/components/shared/  # Reusable SEO component schema
   └─ src/index.ts            # Bootstrap seeding + public permission setup
```

## Run locally

### 1) Start Strapi

```bash
cd backend
npm install
npm run develop
```

Strapi will run on `http://localhost:1337` and seed example records on first startup.

### 2) Start Next.js

```bash
cd frontend
npm install
npm run dev
```

Frontend will run on `http://localhost:3000`.

### 3) Environment variables

Create `frontend/.env.local`:

```env
STRAPI_API_URL=http://localhost:1337/api
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

## Deployment notes

This project is deployment-ready for:

- Frontend: Vercel
- Backend: Railway (recommended) or Render
- Media uploads: Cloudinary

## Production environment variables

### Frontend (`frontend`, Vercel)

```env
STRAPI_API_URL=https://your-strapi-domain.com/api
NEXT_PUBLIC_SITE_URL=https://your-frontend-domain.com
```

### Backend (`backend`, Railway/Render)

```env
NODE_ENV=production
HOST=0.0.0.0
PORT=1337
PUBLIC_URL=https://your-strapi-domain.com
CORS_ORIGIN=https://your-frontend-domain.com
APP_KEYS=longRandom1,longRandom2,longRandom3,longRandom4
API_TOKEN_SALT=longRandomValue
ADMIN_JWT_SECRET=longRandomValue
TRANSFER_TOKEN_SALT=longRandomValue
JWT_SECRET=longRandomValue
ENCRYPTION_KEY=longRandomValue
DATABASE_CLIENT=postgres
DATABASE_URL=postgres://...
DATABASE_SSL=true
DATABASE_SSL_REJECT_UNAUTHORIZED=false
CLOUDINARY_NAME=your_cloud_name
CLOUDINARY_KEY=your_api_key
CLOUDINARY_SECRET=your_api_secret
```

## Deploy backend to Railway

1. Push repository to GitHub.
2. In Railway, create a new project from your GitHub repo.
3. Set **Root Directory** to `backend`.
4. Add a PostgreSQL service in Railway.
5. In backend service variables, set all variables from the backend list above.
6. Ensure `DATABASE_URL` points to Railway Postgres connection string.
7. Deploy service.
8. After first deploy, open `https://your-strapi-domain.com/admin`, create admin account, and verify APIs:
   - `/api/services`
   - `/api/pages`
   - `/api/blog-posts`

## Deploy frontend to Vercel

1. Import the same GitHub repository in Vercel.
2. Set **Root Directory** to `frontend`.
3. Set build command to default (`next build`) and output defaults.
4. Add frontend environment variables:
   - `STRAPI_API_URL=https://your-strapi-domain.com/api`
   - `NEXT_PUBLIC_SITE_URL=https://your-frontend-domain.com` (update after first deploy if needed)
5. Deploy.
6. Update backend `CORS_ORIGIN` with the final Vercel production URL and redeploy backend.

## Cloudinary upload provider (Strapi)

Already configured in `backend/config/plugins.ts` with `@strapi/provider-upload-cloudinary`.

Setup:

1. Create a Cloudinary account.
2. Copy `Cloud name`, `API Key`, and `API Secret`.
3. Set in backend environment:
   - `CLOUDINARY_NAME`
   - `CLOUDINARY_KEY`
   - `CLOUDINARY_SECRET`
4. Redeploy Strapi.
5. In Strapi admin, upload media in Media Library; uploaded files should now be hosted by Cloudinary.

Example usage:

- Attach uploaded media to `Service.image` or `Blog Post.coverImage`.
- Frontend auto-renders returned media URLs from Strapi in `ServiceCard` and detail pages.

## Render alternative (if preferred)

Use same backend env vars; set root to `backend`, add a managed Postgres database, and point `DATABASE_URL` to Render Postgres. Keep `PUBLIC_URL` and `CORS_ORIGIN` aligned with your live domains.
