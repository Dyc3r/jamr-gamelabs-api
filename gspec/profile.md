---
spec-version: v1
---

# Product Profile: Game Labs API

## 1. Product Overview

- **Product name:** Game Labs API
- **Tagline:** The backend system of record for JAMR Game Labs — player profiles, game content, stats, and access control, all in one place.
- **Category:** Backend API / CMS (headless content and data management service)
- **Product type:** Internal platform service — no independent UI, no external customers of its own. It is infrastructure that powers JAMR Game Labs' broader product ecosystem.
- **Current stage:** Concept — foundational specs are being defined before implementation begins.

## 2. Mission & Vision

### Mission Statement

Give JAMR Game Labs a single, reliable backend for managing player identity, game content, gameplay statistics, and content access — so the portal and every game built on top of it can trust one source of truth instead of stitching together ad hoc data stores.

The core problem being solved: game-related player data (profiles, stats, preferences), catalog data (game listings), and access control (entitlements, Patreon-tier gating) previously required bespoke or fragmented handling. This API centralizes that into one CMS-like service with consistent auth, consistent data modeling, and a clean integration surface for the portal.

### Vision Statement

Become the durable backbone that every current and future JAMR game and portal feature can build on without needing to re-solve identity, stats, or entitlements — a system stable and well-modeled enough that it outlives any single game or portal redesign.

## 3. Target Audience

### Primary Users

- **JAMR internal staff** (community managers, support staff, game/content admins) who use admin capabilities — surfaced through the portal's admin UI, which calls this API — to manage player profiles, game listings, stat configuration, and content access.
  - Pain points: fragmented or manual processes for managing entitlements and reconciling Patreon supporter status; inconsistent stat tracking across games.
  - Goals: quickly onboard new games, correct/manage player data, and control who can access what content without touching a database directly.

### Secondary Users

- **Automated systems acting through the portal** — the portal mediates all interaction with this API, including on behalf of individual games. Games never call this API directly; the portal passes callback functions into game runtimes, and those callbacks route through the portal to this API. This includes automated stat submissions (high scores, play time) and entitlement checks driven by gameplay, not just human admin actions.
- **Scheduled/background jobs** — e.g., Patreon supporter sync, that keep entitlement data current without a human in the loop.

### Stakeholders

- **Portal engineering team** — the sole direct API consumer; any change to this API's contract affects them first.
- **Game developers at JAMR** — rely on this API (via the portal) for stat tracking and entitlement checks in their games.
- **Players** — indirect beneficiaries; they never call this API directly, but their profiles, stats, and access rights live here.
- **Patreon supporters** — their pledge tier drives entitlement decisions made by this system.
- **JAMR leadership** — depends on this system to enforce content gating tied to supporter tiers.

## 4. Value Proposition

### Core Value

One authoritative backend for player identity, content, stats, and entitlements — replacing fragmented or ad hoc data handling with a single, consistently modeled system that the portal can integrate against with confidence.

### Key Benefits

- **Single source of truth** for player profiles and game listings, eliminating drift between systems.
- **Unified stat tracking** — both player-specific (high scores, play time, preferences) and system-wide stats — modeled consistently across all games.
- **Centralized entitlements** — one place to control who can access which content, instead of per-game logic.
- **Automatic Patreon-tier sync** — supporter status and patronage tier flow into entitlements without manual reconciliation.
- **Clean integration surface** — the portal is the sole consumer, so the API can optimize for one well-understood contract rather than many.

### Differentiation

Purpose-built for JAMR's actual shape of problem (games + portal + Patreon-gated content) rather than a generic CMS or off-the-shelf entitlements product bolted on after the fact.

## 5. Product Description

### What It Is

A headless backend API and CMS that:
- Manages **player profiles** (identity, preferences).
- Manages **game listings** (the catalog of games under JAMR Game Labs).
- Tracks **stats**, both player-specific (high scores, play time, preferences) and system-wide.
- Enforces **entitlements** — controlling which players can access which content.
- Integrates with **Zitadel** for authentication/identity.
- Integrates with **JAMR's Patreon account** to track current supporters and their patronage tier, feeding that into entitlement decisions.
- Persists to **PostgreSQL**.
- Exposes an **admin surface** that the portal application's admin UI calls to manage all of the above — this API itself has no UI.

Conceptually, the portal is the only thing that talks to this API. It renders the admin CMS interface and handles all player/game-facing interactions, calling this API for reads and writes. When a game needs to report a stat or check access, it goes through a callback the portal provides — the game never calls this API directly.

### What It Isn't

- **Not a player-facing product.** Players never interact with this API directly; the portal is the sole intermediary.
- **Not a general-purpose CMS.** It is scoped specifically to JAMR Game Labs' domain: profiles, games, stats, entitlements, and Patreon-driven access — not arbitrary content types.
- **Not an auth provider.** Identity and authentication are delegated to Zitadel; this API consumes that, it doesn't reimplement it.
- **Not a payments processor.** Patreon integration here is about tracking supporter/tier status for entitlement purposes, not handling payments.

## 6. Use Cases & Scenarios

### Primary Use Cases

1. A JAMR admin creates a new game listing and configures which stats it tracks, via the portal's admin UI.
2. A player's high score or play time is submitted through a game's portal-provided callback and recorded against their profile.
3. A JAMR admin looks up a player profile to troubleshoot an access issue or correct data.
4. Patreon supporter data syncs on a schedule, updating a player's patronage tier and, in turn, their content entitlements.
5. An admin defines which content requires which entitlement (e.g., "supporter-tier-2 and above"), and the API enforces that check on relevant reads.

### Success Stories

Not applicable yet — pre-implementation.

## 7. Market & Competition

Not applicable — this is an internal backend service with no external market or competitors. It does not compete for users; it exists to serve JAMR Game Labs' own portal and games.

## 8. Business Model

Not applicable — internal infrastructure with no direct revenue model or pricing. It has no customers of its own. It does, indirectly, support JAMR's broader business by enforcing Patreon-tier-based content access, but the API itself is not monetized.

## 9. Brand & Positioning

Not applicable in the traditional sense — no external brand, marketing, or public presence. A positioning statement is still useful for internal alignment:

**Positioning Statement:** For JAMR's portal team and internal staff, the Game Labs API is the backend system of record that unifies player profiles, game content, stats, and entitlements — because it's purpose-built around JAMR's actual integration shape (portal-mediated, Patreon-gated) rather than adapted from a generic CMS.

## 10. Success Metrics

### Adoption & Engagement Metrics

- Portal successfully integrated with this API for all profile/stat/entitlement operations.
- API reliability/uptime as experienced by the portal (its only consumer).
- Latency of stat writes and entitlement checks under real game traffic.
- Internal staff satisfaction with the admin capabilities.

### Business Metrics

Not applicable — no independent revenue or paid-user metrics for this service.

### Project Health Metrics

- Patreon sync accuracy (entitlements correctly reflect current supporter tier).
- Zitadel auth integration correctness (no unauthorized access, no false lockouts).

## 11. Public-Facing Information

Not applicable — internal backend service with no public presence.

## 12. Product Roadmap Vision

### Current Focus

Building the core domain: player profiles, game listings, stat tracking (player-specific and system-wide), Zitadel-backed auth, and PostgreSQL persistence.

### Near-Term (Next)

Entitlements/access control, and Patreon integration to sync supporters and patronage tiers into entitlement decisions.

### Long-Term Vision (Later)

Serve as the stable backend for however JAMR Game Labs' catalog of games and the portal evolve, without requiring re-architecture per game or per portal redesign.

## 13. Risks & Assumptions

### Key Assumptions

- The portal remains the sole direct consumer of this API for the foreseeable future; games interact only via portal-provided callbacks.
- Zitadel remains JAMR's auth provider.
- Patreon remains JAMR's supporter/monetization platform and its API stays available for tier sync.

### Risks

- **Coupling risk:** since the portal is the sole consumer, any API contract change requires tight coordination with the portal team.
- **External dependency risk:** correctness of entitlements depends on the reliability and accuracy of the Patreon API sync.
- **Open-design risk:** several data model and API design decisions remain open questions rather than settled ones going into implementation.

### Mitigation Strategies

- Define the API contract with the portal team early and treat it as a first-class spec (see `gspec/architecture.md` once defined).
- Build Patreon sync with clear failure/retry handling so entitlement data degrades gracefully rather than silently, if the Patreon API is unavailable.
