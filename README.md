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

## Go-live checklist

- Set production domains:
  - Frontend: `https://www.yourdomain.com` (Vercel)
  - Backend: `https://cms.yourdomain.com` (Railway/Render)
- Confirm frontend env vars in Vercel:
  - `STRAPI_API_URL=https://cms.yourdomain.com/api`
  - `NEXT_PUBLIC_SITE_URL=https://www.yourdomain.com`
- Confirm backend env vars in Railway/Render:
  - `PUBLIC_URL=https://cms.yourdomain.com`
  - `CORS_ORIGIN=https://www.yourdomain.com`
  - Cloudinary and all Strapi secret keys are present
  - Postgres variables are set and valid
- Redeploy backend first, then redeploy frontend.
- Verify Strapi admin:
  - Open `https://cms.yourdomain.com/admin`
  - Upload an image in Media Library (should store in Cloudinary)
- API smoke tests:
  - `GET https://cms.yourdomain.com/api/services`
  - `GET https://cms.yourdomain.com/api/pages`
  - `GET https://cms.yourdomain.com/api/blog-posts`
  - Ensure responses are `200` and include `data`
- Frontend smoke tests:
  - Home page loads and featured services render
  - Services list and at least one `/services/[slug]` page load
  - Blog list and at least one `/blog/[slug]` page load
  - Contact form submits successfully
- SEO checks:
  - `https://www.yourdomain.com/sitemap.xml` loads
  - `https://www.yourdomain.com/robots.txt` loads
  - Page source contains expected `og:` tags and JSON-LD
- Final hardening:
  - Rotate any placeholder/local secrets
  - Remove temporary/test content
  - Enable monitoring/log alerts in Vercel + Railway/Render

## Launch-day command checklist

Replace domains once, then run:

```bash
# 1) Set your live domains
FRONTEND_URL="https://www.yourdomain.com"
CMS_URL="https://cms.yourdomain.com"

# 2) API health checks (expect HTTP 200)
curl -I "$CMS_URL/api/services"
curl -I "$CMS_URL/api/pages"
curl -I "$CMS_URL/api/blog-posts"

# 3) API content checks (expect JSON containing "data")
curl "$CMS_URL/api/services" | head
curl "$CMS_URL/api/pages" | head
curl "$CMS_URL/api/blog-posts" | head

# 4) Frontend route checks
curl -I "$FRONTEND_URL/"
curl -I "$FRONTEND_URL/services"
curl -I "$FRONTEND_URL/blog"
curl -I "$FRONTEND_URL/contact"

# 5) SEO checks
curl -I "$FRONTEND_URL/sitemap.xml"
curl -I "$FRONTEND_URL/robots.txt"
curl "$FRONTEND_URL/" | head -n 80

# 6) Quick headers sanity
# - Frontend pages should return 200
# - API should return 200 with JSON content type
# - sitemap.xml and robots.txt should return 200
```

Windows PowerShell equivalent:

```powershell
$FRONTEND_URL="https://www.yourdomain.com"
$CMS_URL="https://cms.yourdomain.com"

curl.exe -I "$CMS_URL/api/services"
curl.exe -I "$CMS_URL/api/pages"
curl.exe -I "$CMS_URL/api/blog-posts"

curl.exe -I "$FRONTEND_URL/"
curl.exe -I "$FRONTEND_URL/services"
curl.exe -I "$FRONTEND_URL/blog"
curl.exe -I "$FRONTEND_URL/contact"

curl.exe -I "$FRONTEND_URL/sitemap.xml"
curl.exe -I "$FRONTEND_URL/robots.txt"
```

## Expected outputs

- `GET /api/services`, `GET /api/pages`, `GET /api/blog-posts`
  - Status: `200 OK`
  - Header includes: `content-type: application/json`
  - Body includes: `"data":[...]` (or `"data":[]` if empty)
- Frontend routes (`/`, `/services`, `/blog`, `/contact`)
  - Status: `200 OK`
  - Header includes: `content-type: text/html`
- SEO routes
  - `/sitemap.xml` -> `200 OK`, `content-type: application/xml` (or `text/xml`)
  - `/robots.txt` -> `200 OK`, `content-type: text/plain`
- If you see `401`/`403` on API routes
  - Public permissions are not enabled for `find` and `findOne` in Strapi
- If you see `404` on frontend dynamic pages
  - Confirm Strapi records exist and include valid `slug` values
- If frontend returns `500`
  - Re-check `STRAPI_API_URL`, backend `CORS_ORIGIN`, and backend logs
