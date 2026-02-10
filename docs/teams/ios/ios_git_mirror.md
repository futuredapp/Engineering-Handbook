# Git Mirror to Client's Repository

Some clients require a copy of the source code in their own repository (GitLab, GitHub, Bitbucket, etc.). We use **one-way push mirroring** from GitHub to the client's repository, triggered automatically on every push.

## How it works

Two reusable workflows from [`futuredapp/.github`](https://github.com/futuredapp/.github) handle the mirroring:

| Workflow | Runner | Use case |
|----------|--------|----------|
| `universal-selfhosted-backup.yml` | `self-hosted` | Self-hosted Git server (IP address) |
| `universal-cloud-backup.yml` | `ubuntu-latest` | Cloud-hosted Git (gitlab.com, github.com, etc.) |

Both workflows checkout the repo with full history, set up SSH authentication, add the target remote, and push the current branch.

## Setup guide

### 1. Generate SSH key pair

```bash
ssh-keygen -t ed25519 -C "<project-name>-mirror" -f mirror_key
```

- Use **ed25519** (modern, short, secure). Some older servers may not support it — in that case use `ssh-keygen -t rsa -b 4096` instead.
- The `-C` comment helps identify the key's purpose

This produces two files:

- `mirror_key` — private key (stays with us)
- `mirror_key.pub` — public key (goes to client)

### 2. Add private key to GitHub Secrets

1. Go to GitHub repo → **Settings** → **Secrets and variables** → **Actions**
2. Create new repository secret: **`GITLAB_PUSH_SSH_KEY`**
3. Paste the entire private key content (including `-----BEGIN/END-----` lines)

After adding the key to GitHub Secrets, store both key files in Futured's Bitwarden and delete the local copies.

### 3. Send public key to client

Send the client the **public key** (`mirror_key.pub`) with instructions to add it as a deploy key with write access. The exact steps depend on the platform:

!!! note "Example: GitLab"

    1. Create an empty repository on your GitLab for the mirror (e.g. `group/project-name.git`)
    2. Go to the repository → **Settings** → **Repository** → **Deploy Keys**
    3. Add the provided public key with **write access** enabled
    4. Send us back the SSH clone URL of the repository (e.g. `git@gitlab.example.com:group/project-name.git`)

!!! note "Example: GitHub"

    1. Create an empty repository
    2. Go to the repository → **Settings** → **Deploy keys** → **Add deploy key**
    3. Add the provided public key with **Allow write access** checked
    4. Send us back the SSH clone URL (e.g. `git@github.com:org/project-name.git`)

### 4. Create workflow for branch mirroring

Create `.github/workflows/push_to_mirror.yml`:

```yaml
name: Push to Mirror

on:
  push:
    branches:
      - develop
      - release

jobs:
  push_to_mirror:
    uses: futuredapp/.github/.github/workflows/universal-selfhosted-backup.yml@main
    with:
      host: <HOST>  # IP or domain of client's Git server
      remote: git@<HOST>:group/project-name.git
    secrets:
      SSH_PRIVATE_KEY: ${{ secrets.MIRROR_PUSH_SSH_KEY }}
```

Replace `<HOST>` with the client's server IP or domain.

!!! tip
    Use `universal-cloud-backup.yml` instead of `universal-selfhosted-backup.yml` if the client uses a cloud-hosted service (gitlab.com, github.com, etc.).

### 5. (Optional) Create workflow for tag mirroring

If the client also needs release tags, create a separate workflow for tag mirroring.

Note: unlike branch mirroring, tag mirroring uses an inline workflow (not a reusable one) because it includes conditional logic to only push tags that exist on release branches. See [broker-coach-ios tag workflow](https://github.com/futuredapp/broker-coach-ios/blob/develop/.github/workflows/push_tag_to_gitlab.yml) for a working example.

## Existing setups

| Project | Mirror target | Branches mirrored |
|---------|---------------|-------------------|
| [broker-coach-ios](https://github.com/futuredapp/broker-coach-ios/tree/develop/.github/workflows) | Self-hosted GitLab (`194.228.235.202`) | `develop`, `release`, `release-sk` + tags |
| [broker-coach-api](https://github.com/futuredapp/broker-coach-api/tree/develop/.github/workflows) | Self-hosted GitLab (`194.228.235.202`) | Same setup |
| [alive-app-ios](https://github.com/futuredapp/alive-app-ios/tree/develop/.github/workflows) | Cloud GitLab (`gitlab.com`) | `develop` + releases |

For a full list of projects using the mirror workflow, see [this GitHub search](https://github.com/search?q=org%3Afuturedapp%20universal-selfhosted-backup&type=code).
