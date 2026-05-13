# games-pong

Minimal static AirConsole Pong sample (airconsole-1.11.0).

## Architecture

- **Two-file structure**: `screen.html` (TV, authoritative game state) + `controller.html` (phone input).
- All messages route through AirConsole platform -- no peer-to-peer.
- Screen-authoritative state pattern: screen owns transitions, controllers react via `onCustomDeviceStateChange`.
- State machine: LOBBY <-> GAME on both files, driven by `setCustomDeviceStateProperty("screen", ...)`.
- Master controller pattern: only the master shows the Start button.
- Microphone feature: controller streams RMS audio levels at 10 Hz -> screen adjusts paddle height + color pulse.

## Key AirConsole Patterns

- `silence_inactive_players: true` on screen constructor.
- `setActivePlayers(2)` on game start, `setActivePlayers(0)` on disconnect/lobby.
- `arePlayersSilenced()` guard in controller `onReady` to avoid flashing lobby when silenced.
- `initialized` flag prevents re-triggering lobby on every `onConnect`.
- Message rate limit: 10 msg/s (under platform 25/s cap).
- Delta-time capped at 20ms to prevent physics explosion after tab suspension.
- Complete MediaStream teardown (disconnect nodes, stop tracks, close AudioContext) on disconnect.

## Local Rules

- Treat this as a lightweight manual test fixture, not a framework-heavy app.
- Keep assets browser-friendly and avoid adding build tooling unless the task explicitly requires it.
- Smoke changes by serving the HTML files in a browser or AirConsole-compatible environment.
- Coordinate system: 0-200 x-axis, 0-100 y-axis, normalized to canvas via `zoom = clientHeight / 100`.
- Win condition: score > 3 (i.e. 4 points wins).
