# OpenCode Devcontainer Template

A reusable Dev Container template for an isolated, YOLO-friendly LLM CLI environment with SSH key access disabled and shared auth/config mounts.

It installs OpenCode using the platform-detecting install script and mounts shared config folders for OpenCode, Claude, and Codex.

## Files

```text
opencode-devcontainer/
  .devcontainer/
    devcontainer.json
```

Copy the `.devcontainer` folder into any project where you want this setup.

## What It Does

- Uses the official Ubuntu devcontainer base image.
- Runs as the normal `vscode` user.
- Mounts the current project at `/workspace`.
- Clears `SSH_AUTH_SOCK` inside the container to block SSH agent access.
- Installs OpenCode using the install script instead of npm, avoiding architecture-specific npm package issues.
- Mounts shared auth/config folders for OpenCode, Claude, and Codex.
- Provides passwordless `sudo` for installing extra tools inside the container.

## Host Setup

Set `LLM_AUTH_TOKENS_DIR` on the host to the folder containing your shared LLM auth/config folders.

Linux/macOS:

```bash
export LLM_AUTH_TOKENS_DIR="$HOME/dev/LLM_auth_tokens"
```

That only applies to the current terminal session. To make it permanent, add the export to your shell config.

Bash:

```bash
printf '\nexport LLM_AUTH_TOKENS_DIR="$HOME/dev/LLM_auth_tokens"\n' >> ~/.bashrc
source ~/.bashrc
```

Zsh:

```bash
printf '\nexport LLM_AUTH_TOKENS_DIR="$HOME/dev/LLM_auth_tokens"\n' >> ~/.zshrc
source ~/.zshrc
```

Windows PowerShell:

```powershell
[Environment]::SetEnvironmentVariable(
  "LLM_AUTH_TOKENS_DIR",
  "$env:USERPROFILE\dev\LLM_auth_tokens",
  "User"
)
```

Restart VS Code or your terminal after setting the variable.

## Expected Host Folders

The template expects these folders to exist:

```text
$LLM_AUTH_TOKENS_DIR/codex
$LLM_AUTH_TOKENS_DIR/claude
$LLM_AUTH_TOKENS_DIR/opencode-share
$LLM_AUTH_TOKENS_DIR/opencode-config
```

Create them before starting the devcontainer:

```bash
mkdir -p \
  "$LLM_AUTH_TOKENS_DIR/codex" \
  "$LLM_AUTH_TOKENS_DIR/claude" \
  "$LLM_AUTH_TOKENS_DIR/opencode-share" \
  "$LLM_AUTH_TOKENS_DIR/opencode-config"
```

They are mounted inside the container as:

```text
/home/vscode/.codex
/home/vscode/.claude
/home/vscode/.local/share/opencode
/home/vscode/.config/opencode
```

## Use In A Project

From this repository:

```bash
cp -r opencode-devcontainer/.devcontainer /path/to/your/project/
```

Then open the project in VS Code and run:

```text
Dev Containers: Reopen in Container
```

Check OpenCode inside the container:

```bash
opencode --version
```

## Installing More Tools

The container uses the `vscode` user, but passwordless `sudo` is available:

```bash
sudo apt update
sudo apt install -y ripgrep jq tmux

## Notes

- No Dockerfile is required for this minimal setup.
- No Docker Compose file is required.
- No Ollama host forwarding is configured.
- SSH agent access is disabled by clearing `SSH_AUTH_SOCK`.
- If `LLM_AUTH_TOKENS_DIR` is not set, the bind mounts will fail.
- If the expected host folders do not exist, Docker bind mounts will fail.
- OpenCode is installed through the install script instead of `npm install -g opencode-ai@latest` to avoid cross-architecture npm postinstall issues.