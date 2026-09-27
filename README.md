# Tinker room server (Cloudflare)

A prebuilt Tinker room server — presence, canvas and chat for Tinker's
Rooms — as a Cloudflare Worker with Durable Objects. No Rust toolchain, no
server to run: deploy it and point the app at the address.

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/nullcolor-app/trytinker)

## What you get

A `https://<name>.workers.dev` room server with **guest sign-in on by
default** — anyone with the address can pick a name and join a room they
have an invite to; nobody can sign in as someone else or as a room's owner.
GitHub sign-in and room audio (LiveKit) are optional — see
`.dev.vars.example`.

**Plan:** the free plan works for trying it out. Measured on real
Cloudflare, room traffic (cursors, dragging, chat) costs 0–4 ms of CPU per
message against the free plan's 10 ms per request, but a guest's first
sign-in and the first join to an idle room take 11–14 ms. For real use the
**$5/month Workers plan** removes that ceiling and comfortably covers a
small team.

## Deploy

Click the button above, or by hand:

```bash
npm install
npx wrangler deploy
```

Either way you get a `https://<name>.workers.dev` address. Add secrets
later with `npx wrangler secret put LIVEKIT_URL` (etc.) — see
`.dev.vars.example` for the full list; all of them are optional.

### Custom domain

Cloudflare dashboard -> Workers & Pages -> this Worker -> Settings ->
Domains & Routes -> Add -> Custom Domain (or `npx wrangler deploy --routes
rooms.example.com/*` with a zone already on your Cloudflare account). The
`*.workers.dev` address keeps working either way.

## Point Tinker at it

In the Rooms window's sign-in screen, type the address into "Server
address" and click Connect (or launch with
`--server https://<name>.workers.dev`). An invite link already carries its
server, so anyone you invite doesn't need this step.

## Updating

This repo's contents are replaced wholesale by CI on each Tinker release
(see the main repo's `.github/workflows/cloudflare-deploy-bundle.yml`) — it
isn't meant to be hand-edited. `npx wrangler deploy` again after pulling to
pick up a new version; your rooms, accounts and invites live in this
Worker's Durable Object storage and aren't touched by a redeploy.
