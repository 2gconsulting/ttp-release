# ttp

`ttp` is a command-line tool for interacting with the TrackTics backend API. It is aimed at developers and advanced users who need quick access to authentication, club and tracker listing, and WebSocket telemetry streams.

## Download

Pick the binary that matches your platform from the release assets:

- `ttp_Darwin_arm64`
- `ttp_Darwin_x86_64`
- `ttp_Linux_arm64`
- `ttp_Linux_x86_64`
- `ttp_Windows_arm64`
- `ttp_Windows_x86_64`

After downloading, make the binary executable on macOS or Linux:

```sh
chmod +x ttp
```

## Authentication

Start by logging in:

```sh
ttp login
```

Credentials are cached in `~/.bp/creds.yaml`. If a valid token is close to expiry, `ttp login` extends it automatically.

## Common Commands

- `ttp login` - authenticate with the backend
- `ttp whoami` - print the current authenticated identity
- `ttp clubs` - list clubs available to the current user
- `ttp trackers` - list trackers across accessible clubs
- `ttp ws <tracker-id> <raw|rawinput|pos>` - stream WebSocket data to stdout
- `ttp wsi` - open an interactive WebSocket streaming UI
- `ttp version` - print the CLI version

## Configuration

- `--base-url` or `BP_BASE_URL`: backend API base URL
- `--ws-host` or `BP_WS_HOST`: WebSocket host for `ttp ws`
- `--ws-scheme` or `BP_WS_SCHEME`: WebSocket scheme (`ws` or `wss`)

Default endpoints:

- API: `https://api.bp.stg.2gconsulting.click`
- WebSocket: `wss://ws.bp.stg.2gconsulting.click`

## Examples

```sh
ttp whoami
ttp clubs
ttp trackers
ttp ws 11111111-2222-3333-4444-555555555555 raw
ttp ws 11111111-2222-3333-4444-555555555555 pos --ws-host localhost:8080 --ws-scheme ws
ttp wsi
```

For detailed command help, run:

```sh
ttp --help
ttp ws --help
ttp wsi --help
```
