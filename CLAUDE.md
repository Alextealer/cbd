# CLAUDE.md

## Purpose
Plateforme white-label CBD : les marques créent leur propre ligne CBD
(huiles, fleurs, cosmétiques) sans stock, avec design studio intégré.
Concept inspiré de Selfnamed.com, adapté au marché CBD EU/FR.
Clients cibles : entrepreneurs, boutiques, influenceurs bien-être.

## Repo map
- app/(marketing)/        → pages publiques (homepage, how-it-works, pricing, blog, about)
- app/(studio)/           → design studio (éditeur étiquettes, mockups, export PDF)
- app/(dashboard)/        → espace marque connecté (mes designs, commandes, intégrations)
- app/(admin)/            → back-office interne (produits, orders, users, COA)
- app/api/                → API Routes Next.js (REST + webhooks)
- components/ui/          → shadcn/ui — NE JAMAIS MODIFIER MANUELLEMENT
- components/studio/      → canvas editor, toolbar, template picker, mockup viewer
- components/catalog/     → product cards, filters, COA badge, category nav
- components/marketing/   → hero, pricing table, testimonials, how-it-works
- components/dashboard/   → design list, order tracker, integration cards
- lib/db/                 → schéma Drizzle + migrations PostgreSQL
- lib/stripe/             → paiement (compte CBD high-risk), webhooks
- lib/cbd-compliance/     → validation THC, parsing COA, mentions légales
- lib/email/              → templates Resend (confirmation, shipping, etc.)
- lib/utils/              → fonctions pures sans side effects
- .claude/skills/         → workflows réutilisables (voir chaque SKILL.md)
- .claude/commands/       → /review, /compliance-check, /db-migration
- docs/                   → ADR, ECOSYSTEM.md, CBD_COMPLIANCE.md, RUNBOOK.md

## Tech Stack
- Framework   : Next.js 14 App Router + TypeScript strict
- Styles      : Tailwind CSS v3 + shadcn/ui (radix primitives)
- DB          : PostgreSQL via Supabase + Drizzle ORM
- Auth        : NextAuth.js v5 (credentials + Google OAuth)
- Paiement    : Stripe (compte high-risk CBD) + webhooks
- Storage     : Cloudinary (images, étiquettes, mockups générés)
- Canvas      : Fabric.js v6 (design studio éditeur)
- Email       : Resend + React Email
- Tests       : Vitest (unit) + Playwright (e2e)
- Deploy      : Vercel (Edge runtime pour API publiques)
- Package     : pnpm

## Commands
- pnpm dev              → dev server localhost:3000
- pnpm build            → build production
- pnpm test             → vitest run
- pnpm test:watch       → vitest watch
- pnpm test:e2e         → playwright test
- pnpm db:push          → push schema Drizzle vers Supabase
- pnpm db:generate      → générer migration SQL
- pnpm db:studio        → Drizzle Studio UI
- pnpm lint             → eslint + tsc --noEmit (TOUJOURS avant commit)
- pnpm format           → prettier --write

## Code Style
- TypeScript strict : pas de `any`, pas de `as unknown`, pas de `!`
- ES modules (import/export), jamais CommonJS require()
- Composants : toujours des functional components avec props typées (interface)
- Shadcn : ne jamais modifier /components/ui/, créer des wrappers dans /components/
- Server Components par défaut, 'use client' seulement si interaction utilisateur
- Variables d'env : toujours via lib/env.ts (zod schema validation au démarrage)
- Erreurs API : toujours retourner { error: string, code: string } en JSON
- Fetch : toujours utiliser les Server Actions ou Route Handlers, jamais fetch client direct

## Rules CRITIQUES
- JAMAIS de claims santé sur les produits CBD (DGCCRF/ANSM)
- Toujours afficher le taux THC < 0.3% sur les fiches produit
- Chaque produit DOIT avoir un COA (Certificate of Analysis) lié avant publication
- Stripe webhooks : source de vérité pour les commandes, jamais le retour client
- Pas de `console.log` en production (utiliser lib/logger.ts)
- Migrations Drizzle : jamais modifier le schéma sans générer une migration

## Modules critiques (tester avant tout push)
- lib/cbd-compliance/     → validation légale, tester avec pnpm test
- lib/stripe/             → webhooks paiement, ne pas refactoriser à la volée
- app/api/webhooks/       → critical path, couverture e2e obligatoire
- lib/db/schema.ts        → source de vérité données, ADR requis pour changement

## Références
- docs/CBD_COMPLIANCE.md  → règles légales complètes EU/FR
- docs/ECOSYSTEM.md       → contrat de données (noms de champs, enums)
- docs/RUNBOOK.md         → procédures déploiement et incidents
