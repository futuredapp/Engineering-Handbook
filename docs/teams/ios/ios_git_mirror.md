# Git Mirror to Client's GitLab

Some clients require a copy of the source code in their own GitLab instance. We use **one-way push mirroring** from GitHub to the client's GitLab, triggered automatically on every push.

## How it works

Two reusable workflows from [`futuredapp/.github`](https://github.com/futuredapp/.github) handle the mirroring:

| Workflow | Runner | Use case |
|----------|--------|----------|
| `universal-selfhosted-backup.yml` | `self-hosted` | Self-hosted GitLab (IP address) |
| `universal-cloud-backup.yml` | `ubuntu-latest` | Cloud GitLab (gitlab.com) |

Both workflows checkout the repo with full history, set up SSH authentication, add the GitLab remote, and push the current branch.

## Setup guide

### 1. Generate SSH key pair

```bash
ssh-keygen -t ed25519 -C "<project-name>-gitlab-mirror" -f gitlab_mirror_key
```

- Use **ed25519** (modern, short, secure). Some older GitLab instances may not support it — in that case use `ssh-keygen -t rsa -b 4096` instead.
- The `-C` comment helps identify the key's purpose

This produces two files:

- `gitlab_mirror_key` — private key (stays with us)
- `gitlab_mirror_key.pub` — public key (goes to client)

### 2. Add private key to GitHub Secrets

1. Go to GitHub repo → **Settings** → **Secrets and variables** → **Actions**
2. Create new repository secret: **`GITLAB_PUSH_SSH_KEY`**
3. Paste the entire private key content (including `-----BEGIN/END-----` lines)

After adding the key to GitHub Secrets, store both key files in Futured's Bitwarden and delete the local copies.

### 3. Send public key to client

Send the client the **public key** (`gitlab_mirror_key.pub`) with these instructions:

!!! note "Instructions for the client"

    1. Create an empty repository on your GitLab for the mirror (e.g. `group/project-name.git`)
    2. Go to the repository → **Settings** → **Repository** → **Deploy Keys**
    3. Add the provided public key with **write access** enabled
    4. Send us back the SSH clone URL of the repository (e.g. `git@gitlab.example.com:group/project-name.git`)

### 4. Create workflow for branch mirroring

Create `.github/workflows/push_to_gitlab.yml`:

```yaml
name: Push to GitLab

on:
  push:
    branches:
      - develop
      - release

jobs:
  push_to_gitlab:
    uses: futuredapp/.github/.github/workflows/universal-selfhosted-backup.yml@main
    with:
      host: <GITLAB_HOST>  # IP or domain of client's GitLab
      remote: git@<GITLAB_HOST>:group/project-name.git
    secrets:
      SSH_PRIVATE_KEY: ${{ secrets.GITLAB_PUSH_SSH_KEY }}
```

Replace `<GITLAB_HOST>` with the client's GitLab IP or domain.

!!! tip
    Use `universal-cloud-backup.yml` instead of `universal-selfhosted-backup.yml` if the client uses cloud GitLab (gitlab.com).

### 5. (Optional) Create workflow for tag mirroring

If the client also needs release tags, create `.github/workflows/push_tag_to_gitlab.yml`.

Note: unlike branch mirroring, tag mirroring uses an inline workflow (not a reusable one) because it includes conditional logic to only push tags that exist on release branches. See [broker-coach-ios tag workflow](https://github.com/futuredapp/broker-coach-ios/blob/develop/.github/workflows/push_tag_to_gitlab.yml) for a working example.

## Existing setups

| Project | GitLab type | Branches mirrored |
|---------|-------------|-------------------|
| [broker-coach-ios](https://github.com/futuredapp/broker-coach-ios/tree/develop/.github/workflows) | Self-hosted (`194.228.235.202`) | `develop`, `release`, `release-sk` + tags |
| [broker-coach-api](https://github.com/futuredapp/broker-coach-api/tree/develop/.github/workflows) | Self-hosted (`194.228.235.202`) | Same setup |
| [alive-app-ios](https://github.com/futuredapp/alive-app-ios/tree/develop/.github/workflows) | Cloud (`gitlab.com`) | `develop` + releases |
