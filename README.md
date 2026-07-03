# TF Workout Dashboard v24 — Live Client Sync

This build keeps the Workout Builder as the admin source of truth and publishes only client-safe, view-only workout assignments to Netlify Blobs.

## What is live

- Admin edits Workout Builder or imports a TXT backup.
- Local Board saves immediately in the browser.
- A publish attempt is queued after saves/imports; manual publishing is always available at **Menu → Publish Client Workouts**.
- Published client assignments and workout boards live in **Netlify Blobs**.
- The client portal polls for fresh programming every 20 seconds and refreshes automatically when the coach publishes.
- Client route: `/client`
- Demo account: Jordan Roman / `JRoman01`

## Secure deployment configuration

1. Deploy this folder to a Netlify site connected to your Git repository.
2. In **Netlify → Site configuration → Environment variables**, add:

```text
ADMIN_PUBLISH_SECRET=<use a long random secret; do not use the dashboard password>
```

3. Redeploy after adding the variable.
4. In the admin dashboard, choose **Menu → Publish Client Workouts**. On the first publish in a browser session, enter the same `ADMIN_PUBLISH_SECRET`. It is held only in that browser tab/session storage, never embedded in the site files or published client data.
5. Use **Menu → Client Accounts → Save & Publish** after changing client names, PINs, or workout assignments.

## Client PIN design

- Client PINs are sent only during publishing and are stored in Blobs as salted `scrypt` hashes.
- The `/client` login endpoint validates the PIN server-side.
- The client receives only its assigned workout week and assigned program, never the client account list or other clients’ PINs.
- Use unique PINs. Treat a PIN as a convenience login, not an identity-proofing system.

## Important access boundary

The existing `Admin1999 / jaxroman` dashboard login is a UI gate. It is not sufficient as a production authorization layer on its own.

For a stronger production admin boundary, enable Netlify Identity with invite-only access or place the dashboard behind an access control layer. The server-side `ADMIN_PUBLISH_SECRET` still protects the Blobs publishing endpoint even if someone can load the static dashboard page.

## Operational workflow

1. Make programming changes in **Workout Builder**.
2. Confirm Local Board displays a green saved/sync status.
3. Publish manually once after meaningful edits or use the queued auto-publish.
4. Client portals update within roughly 20 seconds while open; a refresh/login always pulls the newest published board.
5. Export TXT periodically as an offline recovery backup.
