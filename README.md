# NeoNetrek — Deploy to Fly.io

Deploy your own NeoNetrek server on [Fly.io](https://fly.io) in minutes. No compilation — this repo layers your config on top of a pre-built Docker image.

## Steps

### 1. Fork this repo

Click **Fork** on GitHub.

### 2. Edit your config

**`config.js`** — set your server name, location, admin info, and message of the day.

**`fly.toml`** — change `app` to your chosen app name and `primary_region` to a [Fly region](https://fly.io/docs/reference/regions/) near your players.

### 3. Deploy

```bash
fly auth login
fly launch          # say Yes to use the existing fly.toml
fly deploy
```

Visit `https://<your-app>.fly.dev` — you should see the portal. Click **Play Now** to launch the game.

### 4. Get listed

Open a PR to [neonetrek/neonetrek.github.io](https://github.com/neonetrek/neonetrek.github.io) adding your server to `servers.json`. Once merged, it appears on all NeoNetrek portals automatically.

See [HOSTING.md](https://github.com/neonetrek/client-server/blob/main/HOSTING.md) for field reference and listing guidelines.

## Tips

- Scale up for more players: `fly scale vm shared-cpu-2x`
- Monitor: `fly logs`, `fly status`
- Custom domain: `fly certs add your.domain.com`
- Pin to a specific release: change `FROM ghcr.io/neonetrek/client-server:main` to `:v1.0.0` in the Dockerfile.
