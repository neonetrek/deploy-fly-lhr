# NeoNetrek — Deploy to Fly.io

Deploy your own NeoNetrek server on [Fly.io](https://fly.io) in minutes. No compilation — this repo layers your config on top of a pre-built Docker image.

## Steps

### 1. Fork this repo

Click **Fork** on GitHub.

### 2. Deploy on Fly.io

Edit **`fly.toml`** — change `app` to your chosen app name and `primary_region` to a [Fly region](https://fly.io/docs/reference/regions/) near your players.

```bash
fly auth login
fly launch          # say Yes to use the existing fly.toml
fly deploy
```

### 3. Add persistent storage

Create a volume for player accounts, stats, and game state so they survive redeploys and restarts:

```bash
fly volumes create netrek_data --region <your-region> --size 1
fly scale count 1   # volumes can only attach to one machine
fly deploy          # redeploy to mount the volume
```

### 4. Update your config

Once your app is live at `https://<your-app>.fly.dev`, edit **`config.js`**:

- Set `serverHost` to `<your-app>.fly.dev:2592`
- Set `wsProxy` to `wss://<your-app>.fly.dev/ws`
- Update server name, location, admin info, etc.

Push the changes — Fly auto-redeploys.

### 5. Get listed

Open a PR to [neonetrek/neonetrek.github.io](https://github.com/neonetrek/neonetrek.github.io) adding your server to `servers.json`. Once merged, it appears on all NeoNetrek portals automatically.

See [HOSTING.md](https://github.com/neonetrek/client-server/blob/main/HOSTING.md) for field reference and listing guidelines.

## Persistent data

The volume mounted at `/opt/netrek/var` stores:

| File | Description |
|------|-------------|
| `players` | Player accounts and lifetime stats (binary flat file) |
| `players.index` | GDBM index for fast player lookups by name |
| `scores` | Player rankings |
| `planets` | Planet ownership state |
| `global` | Global server state |

This data survives deploys and restarts. Without a volume, all player data is lost on every redeploy.

Back up with `fly ssh console -C "tar czf - /opt/netrek/var" > backup.tar.gz`.

## Tips

- Monitor: `fly logs`, `fly status`
- Custom domain: `fly certs add your.domain.com`
- Pin to a specific release: change `FROM ghcr.io/neonetrek/client-server:main` to `:v1.0.0` in the Dockerfile.
