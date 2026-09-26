# Implementation Plan
## Smart Local Tourism Business Portal

A week-by-week plan turning `PRD.md`, `TRD.md`, `APP_FLOW.md`, `UI_UX_BRIEF.md`, and
`BACKEND_SCHEMA.md` into working software. Adjust week counts to your actual timeline (semester
length, team size).

---

## Phase 0 — Setup (Week 1)

**Goal:** everyone can run the project locally.

- [ ] Initialize Spring Boot project (Spring Web, Spring Data JPA, MySQL Driver, Spring Security)
- [ ] Create MySQL database, run schema from `BACKEND_SCHEMA.md` §2
- [ ] Set up Clerk project (test instance), get publishable/secret keys
- [ ] Set up Supabase project, create storage bucket
- [ ] Set up environment variable handling (`application-local.properties`, `.gitignore` it)
- [ ] Confirm `README.md` setup steps actually work on a clean machine

**Deliverable:** empty Spring Boot app running, connected to MySQL, "Hello World" endpoint live.

---

## Phase 1 — Auth (Week 2)

- [ ] Integrate Clerk's JS sign-in/sign-up widget on the frontend
- [ ] Build `ClerkAuthFilter` to verify JWTs against Clerk's JWKS endpoint
- [ ] On first authenticated request (or via Clerk webhook), create the local `users` row with role
- [ ] Build role selection step (Tourist vs Business Owner) at signup
- [ ] Protect routes by role (Spring Security config)

**Deliverable:** can sign up as either role, log in/out, and hit a protected "who am I" endpoint
that returns the correct role.

**Fallback checkpoint:** if Clerk integration is taking too long, switch to Spring Security +
BCrypt-hashed local passwords (documented as a scope decision — see PRD §11).

---

## Phase 2 — Business Listings (Weeks 3–4)

- [ ] `BusinessListing` entity, repository, service, controller (CRUD)
- [ ] Image upload endpoint → Supabase Storage → save URLs in `listing_images`
- [ ] Availability management (set/view available dates)
- [ ] Business Owner dashboard UI: "My Listings", "Add New Listing" form
- [ ] Enforce: new/edited listings start as `PENDING`

**Deliverable:** a Business Owner can create a listing with photos and see it sitting in
"Pending" status.

---

## Phase 3 — Public Discovery (Week 5)

- [ ] Public `/api/listings` with filters (category, location, price)
- [ ] Homepage with featured/category sections
- [ ] Browse/search page with filter UI (per `UI_UX_BRIEF.md` §3.2)
- [ ] Listing detail page (photos, description, availability, reviews placeholder)

**Deliverable:** anyone (logged in or not) can browse and view approved listings — but there won't
be any yet until Phase 4's admin approval exists, so seed 2–3 listings manually as `APPROVED` for
testing.

---

## Phase 4 — Admin Approval (Week 6)

- [ ] Admin role + protected admin routes
- [ ] Pending listings queue UI
- [ ] Approve/Reject actions (with optional reason)
- [ ] Basic stats endpoint + dashboard cards

**Deliverable:** full loop works — Owner creates listing → Admin approves → listing appears
publicly.

---

## Phase 5 — Booking Flow (Weeks 7–8)

- [ ] `Booking` entity, repository, service, controller
- [ ] Availability check before allowing a booking request
- [ ] Tourist: submit booking, view "My Bookings", cancel a booking
- [ ] Business Owner: view incoming requests, Accept/Reject
- [ ] Status transitions enforced server-side (per `APP_FLOW.md` §3–4)
- [ ] Notifications on state changes (in-app minimum; email if time allows)

**Deliverable:** a Tourist can book a date, the Owner can confirm/reject it, and both sides see
correct status.

---

## Phase 6 — Reviews (Week 9)

- [ ] Job/check to flip `CONFIRMED` → `COMPLETED` once the booking date has passed
- [ ] `Review` entity, repository, service, controller
- [ ] "Leave a Review" UI on completed bookings
- [ ] Display reviews + average rating on listing detail page

**Deliverable:** full feature set from PRD is functionally complete.

---

## Phase 7 — Polish & Pre-Launch (Weeks 10–11)

- [ ] Apply full Tailwind styling per `UI_UX_BRIEF.md` (colors, spacing, responsive behavior)
- [ ] Seed realistic demo data (multiple categories, images, a few reviews)
- [ ] Work through `PRE_LAUNCH_CHECKLIST.md` item by item
- [ ] Cross-browser / mobile responsiveness check
- [ ] Write/finalize project report referencing these docs

**Deliverable:** demo-ready application.

---

## Phase 8 — Testing & Demo Prep (Week 12)

- [ ] Manual end-to-end run-through of every flow in `APP_FLOW.md`
- [ ] Fix any broken edge cases (double-booking, reviewing before completion, editing others'
      listings, etc. — see TRD §6 Acceptance Criteria)
- [ ] Prepare demo script / seed accounts for each role (Tourist, Owner, Admin)
- [ ] Rehearse viva explanation of architecture and key design decisions

---

## Testing Checklist (run before each phase's "done")

- Can a user in the wrong role access a protected action? (should be blocked)
- Does the UI ever trust client-side validation alone for something security-relevant?
- Do all foreign-key relationships behave correctly on delete (e.g., what happens to bookings if a
  listing is deleted — decide and document a policy: soft-delete listings instead of hard delete)?
- Does every state-changing action produce visible feedback in the UI?
