# aibox

Keeps Claude Code and the ChatGPT desktop app on this machine updated, signed in and
reachable from other devices, with a few short commands over SSH.

## Install

```bash
git clone <this repo> && ln -s "$PWD/aibox/aibox" ~/.local/bin/aibox
aibox setup        # once, asks for your sudo password
```

Needs Linux with systemd, apt, `jq`, `curl` and `python3`.

## Use

```
aibox                      status: signed in, versions, updates, servers, busy sessions
aibox update [AGENT]       update, then restart whatever runs an old version
aibox auth [AGENT]         sign in whatever is signed out (a link and a code, no desktop needed)
aibox rc [AGENT] [DIR]     claude: serve DIR (default: here) to claude.ai/code and the Claude app
                           chatgpt: keep the app running with remote access on; --pair for a code
aibox rc rm NAME|DIR       stop serving a folder
aibox restart [AGENT] [--now]
aibox help
```

AGENT is `claude` or `chatgpt`; without it, both.

## How it works

- Each Remote Control server is a systemd user unit, `claude-rc@NAME`, running
  `~/.config/aibox/rc/NAME.sh`. Edit that file to change its flags.
- Restarts wait until a server's sessions are idle; the sessions come back afterwards.
  `--now` skips the wait.
- `setup` starts the servers at boot, lets `aibox update` upgrade ChatGPT through apt
  without a password (only that), and moves servers started by hand under aibox.
- `aibox rc` marks the folder as trusted in Claude, which Remote Control requires.
- Sign-in checks ask Anthropic and OpenAI directly. If the saved key is refused, aibox
  makes a small request through the agent itself, which renews a stale key.
- Some checks read Claude's and Codex's internal files, so an update to either can break one.

## TODO

- `aibox install [AGENT]`: install Claude Code and the ChatGPT app, so `setup` covers a
  fresh machine.

## See also

[yolovm](https://github.com/tochkov/yolovm): Ubuntu desktop VMs for coding agents. aibox
looks after the machine itself.
