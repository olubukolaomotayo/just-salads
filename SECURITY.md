# Security notes, Just Salads (synced)

The synced app is protected in layers. No single thing is trusted on its own.

## 1. Login (who gets in)
- Email + password (Firebase Authentication). Chosen for reliability on iOS, where Google sign-in loops due to Safari/WebKit cross-site storage rules (affects Chrome on iOS too, since it uses WebKit).
- The phone saves the password in the device keychain, so unlock is still a Face ID glance.
- Use a strong, unique password. Password reset by email is available. True 2FA is not on the free tier; can be added later (paid Identity Platform or a same-domain hosting move to re-enable Google).

## 2. Database rules (what they can touch) — REQUIRED
The Firebase web config in `sync.html` is **not a secret** (Google designs it to be public). Real protection is the Firestore rules in `firestore.rules`, which must be pasted into the Firebase console:
- A signed-in user can read/write **only their own** document (`request.auth.uid == userId`).
- Everything else is denied by default.
- Result: even someone with the public link and the config cannot read the data without being logged in as the owner.

## 3. Transport & code
- Served over HTTPS (GitHub Pages).
- Firebase SDK loaded from Google's official CDN, pinned to a fixed version (10.12.0), so the dependency can't silently change.
- All user-entered text is HTML-escaped before display, to prevent script injection.
- No secrets, keys, recipes, or prices are committed to the repo.

## 4. Recommended console hardening (owner)
- Authentication → Settings → Authorized domains: keep only `localhost` and the app's GitHub Pages domain. Remove others.
- Enable only the Google sign-in provider; leave the rest disabled.
- Optional next step: turn on Firebase App Check to block API abuse from other origins.

## Honest note
"Hacker-proof" is not something anyone can truthfully promise. What this setup gives you is layered, industry-standard protection (per-user access control, enforced login, HTTPS, no secrets in code). That is the right bar for a small business app, and we can add more (App Check, audit logging) if the business grows.
