---
title: Patterns Next.js App Router pour la gestion d'URLs
impact: HIGH
impactDescription: Guide consolidé des conventions Next.js officielles pour routing, metadata, redirects et SEO.
tags: nextjs, app-router, routing, seo, metadata, typescript
---

# Patterns Next.js App Router

Guide de référence des conventions officielles Next.js pour l'implémentation d'URLs.

---

## 1) Structure des fichiers App Router

```
app/
├── layout.tsx          # Root layout (obligatoire)
├── page.tsx            # Page d'accueil /
├── not-found.tsx       # Page 404 globale
├── robots.ts           # robots.txt dynamique
├── sitemap.ts          # sitemap.xml dynamique
├── [scope]/
│   ├── layout.tsx      # Layout par scope
│   ├── page.tsx        # Index du scope
│   └── [channel]/
│       ├── page.tsx    # Index du channel
│       └── [slug]/
│           └── page.tsx  # Page de contenu
└── api/
    └── [scope]/
        └── [resource]/
            └── route.ts  # API endpoints
```

---

## 2) Dynamic Segments

### Segment simple `[slug]`

```typescript
// app/[scope]/[channel]/[slug]/page.tsx
export default async function Page({
  params,
}: {
  params: Promise<{ scope: string; channel: string; slug: string }>
}) {
  const { scope, channel, slug } = await params
  return <div>{scope}/{channel}/{slug}</div>
}
```

### Catch-all `[...slug]`

Capture tous les segments suivants (au moins 1 requis).

```typescript
// app/docs/[...slug]/page.tsx
// Matche: /docs/a, /docs/a/b, /docs/a/b/c
export default async function Page({
  params,
}: {
  params: Promise<{ slug: string[] }>
}) {
  const { slug } = await params
  // slug = ['a'] ou ['a', 'b'] ou ['a', 'b', 'c']
  return <div>Path: {slug.join('/')}</div>
}
```

### Optional catch-all `[[...slug]]`

Comme catch-all mais matche aussi la route sans segment.

```typescript
// app/shop/[[...slug]]/page.tsx
// Matche: /shop, /shop/a, /shop/a/b
export default async function Page({
  params,
}: {
  params: Promise<{ slug?: string[] }>
}) {
  const { slug } = await params
  // slug = undefined ou ['a'] ou ['a', 'b']
  return <div>Path: {slug?.join('/') ?? 'index'}</div>
}
```

---

---

## 3) Paramètres d'URL (Async Params & SearchParams)

Depuis Next.js 15+, `params` et `searchParams` sont des **Promesses**.

### Dynamic Segments + SearchParams

```typescript
// app/shop/[category]/page.tsx
export default async function Page({
  params,
  searchParams,
}: {
  params: Promise<{ category: string }>
  searchParams: Promise<{ sort?: string; page?: string }>
}) {
  // 1. Await obligatoire
  const { category } = await params
  const { sort = 'date', page = '1' } = await searchParams
  
  return (
    <div>
      Category: {category}
      Sort: {sort}
      Page: {page}
    </div>
  )
}
```

### Typed Routes (Next.js 16)

Pour activer l'autocomplétion et la validation des liens `<Link href="...">`.

```typescript
// next.config.ts
const nextConfig = {
  experimental: {
    typedRoutes: true,
  },
}
```

Si activé, le `URL Builder` doit retourner le type `Route` :

```typescript
import type { Route } from 'next'

function buildUrl(...): Route {
  return '/valid/path' as Route
}
```

---

## 4) Route Groups `(folder)`

Organise le code sans affecter l'URL.

```
app/
├── (marketing)/
│   ├── layout.tsx      # Layout marketing
│   ├── pricing/page.tsx  # /pricing
│   └── about/page.tsx    # /about
└── (app)/
    ├── layout.tsx      # Layout app
    └── dashboard/page.tsx  # /dashboard
```

---

## 4) Proxy (ex-Middleware) - Next.js 16+

Depuis Next.js 16, `middleware.ts` est remplacé par `proxy.ts`.

```typescript
// proxy.ts (à la racine ou dans src/)
import { type NextRequest, NextResponse } from 'next/server'

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
}

export default async function proxy(request: NextRequest) {
  const { pathname } = request.nextUrl
  
  // Redirect (307)
  if (pathname === '/old') {
    return NextResponse.redirect(new URL('/new', request.url))
  }
  
  // Rewrite (transparent)
  if (pathname.startsWith('/rewrite')) {
    return NextResponse.rewrite(new URL('/destination', request.url))
  }
  
  // Headers modification
  const response = NextResponse.next()
  response.headers.set('x-custom-header', 'value')
  return response
}
```

---

## 5) Parallel Routes (`@folder`)

Permet de rendre plusieurs pages simultanément dans le même layout (ex: dashboard complexe).

```
app/
├── layout.tsx
├── @analytics/
│   ├── page.tsx       # rendu dans props.analytics
│   └── default.js     # fallback si slot inactif
└── @team/
    └── page.tsx       # rendu dans props.team
```

```typescript
// app/layout.tsx
export default function Layout({
  children,
  analytics,
  team
}: {
  children: React.ReactNode
  analytics: React.ReactNode
  team: React.ReactNode
}) {
  return (
    <>
      {children}
      <div className="dashboard-grid">
        {analytics}
        {team}
      </div>
    </>
  )
}
```

---

## 6) Intercepting Routes (`(.)folder`)

Permet de charger une route dans le layout actuel (ex: modale) tout en gardant l'URL accessible directement.

```
app/
├── feed/
│   └── page.tsx
├── photo/[id]/
│   └── page.tsx       # Page photo pleine (accès direct)
└── feed/
    └── (..)photo/[id]/
        └── page.tsx   # Modale interceptée (depuis feed)
```

- `(.)` : même niveau
- `(..)` : un niveau au-dessus
- `(..)(..)` : deux niveaux au-dessus
- `(...)` : depuis la racine `app/`

---

## 7) notFound() - Validation côté serveur

```typescript
// app/[scope]/[channel]/page.tsx
import { notFound } from 'next/navigation'

const VALID_SCOPES = ['pulse-crm', 'dev-web']
const VALID_CHANNELS = ['docs', 'marketing', 'support']

export default async function Page({
  params,
}: {
  params: Promise<{ scope: string; channel: string }>
}) {
  const { scope, channel } = await params
  
  // Validation scope/channel
  if (!VALID_SCOPES.includes(scope) || !VALID_CHANNELS.includes(channel)) {
    notFound() // Renvoie 404, pas besoin de return
  }
  
  return <div>{scope}/{channel}</div>
}
```

---

## 8) generateMetadata - SEO

```typescript
// app/[scope]/[channel]/[slug]/page.tsx
import type { Metadata } from 'next'

export async function generateMetadata({
  params,
}: {
  params: Promise<{ scope: string; channel: string; slug: string }>
}): Promise<Metadata> {
  const { scope, channel, slug } = await params
  const canonicalUrl = `https://example.com/${scope}/${channel}/${slug}`
  
  return {
    title: `${slug} | ${scope}`,
    alternates: {
      canonical: canonicalUrl,
    },
    robots: {
      index: true,
      follow: true,
      googleBot: {
        index: true,
        follow: true,
        'max-image-preview': 'large',
      },
    },
  }
}
```

### Metadata noindex pour routes privées

```typescript
// app/(app)/dashboard/page.tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  robots: {
    index: false,
    follow: false,
  },
}
```

---

## 9) robots.ts

```typescript
// app/robots.ts
import type { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      {
        userAgent: '*',
        allow: '/',
        disallow: ['/api/', '/admin/', '/preview/', '/_next/'],
      },
    ],
    sitemap: 'https://example.com/sitemap.xml',
  }
}
```

---

## 10) sitemap.ts

```typescript
// app/sitemap.ts
import type { MetadataRoute } from 'next'

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const baseUrl = 'https://example.com'
  
  // Pages statiques
  const staticPages = [
    { url: baseUrl, lastModified: new Date(), priority: 1 },
    { url: `${baseUrl}/about`, lastModified: new Date(), priority: 0.8 },
  ]
  
  // Pages dynamiques (depuis DB/CMS)
  const posts = await getPosts()
  const dynamicPages = posts.map((post) => ({
    url: `${baseUrl}/blog/${post.slug}`,
    lastModified: post.updatedAt,
    changeFrequency: 'weekly' as const,
    priority: 0.6,
  }))
  
  return [...staticPages, ...dynamicPages]
}
```

---

## 11) Link Component avec prefetch

```typescript
// components/nav.tsx
import Link from 'next/link'
import { buildUrl } from '@/lib/url'

export function Nav() {
  return (
    <nav>
      {/* Prefetch auto (viewport + static) */}
      <Link href={buildUrl({ scope: 'pulse-crm', channel: 'docs' })}>
        Documentation
      </Link>
      
      {/* Désactiver prefetch pour routes lourdes */}
      <Link href="/heavy-page" prefetch={false}>
        Page lourde
      </Link>
      
      {/* Remplacer l'historique (pas de back) */}
      <Link href="/dashboard" replace>
        Dashboard
      </Link>
    </nav>
  )
}
```

---

## 12) URL Builder centralisé

```typescript
// lib/url.ts
type BuildUrlParams = {
  scope: string
  channel?: string
  slug?: string
  locale?: string
}

const BASE_URL = process.env.NEXT_PUBLIC_BASE_URL || 'https://example.com'

export function buildUrl({ scope, channel, slug, locale }: BuildUrlParams): string {
  const segments = [scope, channel, slug].filter(Boolean)
  const path = `/${segments.join('/')}`
  
  if (locale && locale !== 'fr') {
    return `/${locale}${path}`
  }
  
  return path
}

export function buildAbsoluteUrl(params: BuildUrlParams): string {
  return `${BASE_URL}${buildUrl(params)}`
}
```

---

## Références officielles

- [Routing](https://nextjs.org/docs/app/building-your-application/routing)
- [Dynamic Routes](https://nextjs.org/docs/app/api-reference/file-conventions/dynamic-routes)
- [redirects](https://nextjs.org/docs/app/api-reference/config/next-config-js/redirects)
- [generateMetadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata)
- [robots.txt](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots)
- [sitemap.xml](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap)
- [notFound](https://nextjs.org/docs/app/api-reference/functions/not-found)
- [Link](https://nextjs.org/docs/app/api-reference/components/link)
