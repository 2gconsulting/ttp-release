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
- `ttp completion <shell>` - generate shell completion scripts

## WebSocket Data Channels

You can stream real-time telemetry from a specific tracker using the `ttp ws` command:

```sh
ttp ws <tracker-id> <channel>
```

The `<channel>` argument determines the type of telemetry data you receive:

- **`pos`**: Processed position data.
- **`rawinput`**: Unprocessed, base64-encoded raw data sent immediately upon receipt from the tracker, regardless of processing status. If a processing error occurs (e.g., due to an unsupported data format), the error reported by the backend will be included here. **Use this channel when adapting new data formats to see backend processing errors.**
- **`raw`**: Base64-encoded raw data that has been successfully decoded and sequenced. If packets arrive out of order, the backend reorders them and delivers them in the correct sequence on this channel.

### Message JSON Format

Messages received over the WebSocket are formatted as JSON objects. A typical message payload includes telemetry data, device metadata, and a timestamp:

```json
{
  "raw_imu": "<base64_string>",
  "position": { ... },
  "tracker_id": "<uuid>",
  "device_id": "<uuid>",
  "thing_name": "<string>",
  "timestamp": "<iso8601_datetime>",
  "message_index": 0,
  "message_count": 2,
  "error": "<error_message>"
}
```

**Handling Large Messages**

WebSocket messages have a size limit. If a payload—such as a large array of positions or an extensive base64 raw string—exceeds this limit, the backend automatically splits it into multiple smaller chunks.

When a message is chunked, the payload includes two specific fields:
- `message_count`: The total number of chunks the original message was split into.
- `message_index`: The 0-based sequence index of the current chunk.

To reconstruct the complete payload, clients should buffer incoming chunks that share the same `timestamp` and concatenate their primary data elements (i.e., appending `raw_imu` strings or merging `position.Positions` arrays) sequentially based on the `message_index`. If a message fits within the size limit and is not chunked, these fields are omitted for backward compatibility.

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
