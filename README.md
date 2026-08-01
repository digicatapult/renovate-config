# Renovate Config

## Usage

### Organisation setup
Get a github orgnisation administrator to:
- Ensure renovatebot is added with [access to the repository](https://github.com/apps/renovate) in the org.

- Ensure renovate approver is added with [access to the repository](https://github.com/apps/renovate-approve) in the org.

### Repository Setup

Ensure that the repository has the following branch protection settings:

- [x] Require a pull request before merging
	- [x] Require Approvals (1)
	- [x] Dismiss stale pull request approvals when new commits are pushed
	- [ ] Require review from Code Owners
	- [ ] Restrict who can dismiss pull request reviews
	- [x] Allow specified actors to bypass required pull requests Specify
		- [Renovate](https://github.com/apps/renovate/installations/32996850).
	- [x] Require approval of the most recent reviewable push
- [x] Require status checks to pass before merging (If you have status checks)
	- [x] Require branches to be up to date before merging
	- Run lint
	- Run tests
	- Check Version
	- Run dependency check
	- Build docker image
- [x] Require conversation resolution before merging

### Codebase

Add this into your application repositories `renovate.json`:

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": ["github>digicatapult/renovate-config"]
}
```

#### Terraform / Terragrunt repositories

For repositories containing Terraform or Terragrunt infrastructure code (e.g. `*-tf-infra`, `*-terragrunt-infra`), use the `terraform` preset instead:

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": ["github>digicatapult/renovate-config:terraform"]
}
```

This preset enables managers for:

- `.tf` files — Terraform providers, registry modules, and `required_version` constraints
- `terragrunt.hcl` files — Terragrunt module sources
- `.terraform-version` and `.terragrunt-version` — CLI version pinning (asdf/tfenv style)
- GitHub Actions workflows (inherited from the default preset)

Behaviour:

- **No automerge** for any Terraform-related update — every change requires a human review of the resulting `terraform plan`.
- **Grouped PRs** — minor and patch updates for providers / modules are batched into a single PR per ecosystem to reduce noise; major updates get their own PR labeled `major-update`.
- **Business hours only** — PRs are opened Monday–Friday, 09:00–17:00 (Europe/London) so reviewers are available.
- **Rate limited** — at most 2 new PRs per hour and 5 concurrent open PRs.
- **3-day stability wait** for grouped minor/patch updates to avoid pulling in immediately-yanked releases.

#### Flux repositories

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": ["github>digicatapult/renovate-config:flux"]
}
```

#### Helm chart repositories

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": ["github>digicatapult/renovate-config:helm"]
}
```

#### Repositories using a GitHub merge queue

Repositories on a merge queue need four changes to how Renovate behaves. Apply the `merge-queue` overlay **after** the base preset so its overrides win:

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": [
		"github>digicatapult/renovate-config",
		"github>digicatapult/renovate-config:merge-queue"
	]
}
```

It composes with the language presets in the same way, base first:

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": [
		"github>digicatapult/renovate-config:poetry",
		"github>digicatapult/renovate-config:merge-queue"
	]
}
```

Unlike the language presets, `merge-queue` deliberately does **not** extend the base preset itself. It is orthogonal to language, so it is an overlay rather than a variant.

What it changes and why:

| Setting | Base | Merge queue | Why |
| --- | --- | --- | --- |
| `bumpVersion` | `patch` | unset | Renovate writes the next version into the package file on its own branch. Two Renovate pull requests therefore compute the same version, and the second to reach the queue fails the version check and is evicted. Under merge queue versioning the number is applied on trunk after merge instead. |
| `addLabels` | none | `v:patch` | Merge queue repositories gate pull requests on carrying exactly one `v:` label. Without this every Renovate pull request fails that gate and can never merge. |
| `platformAutomerge` | Renovate default | `true` | Uses GitHub's own auto-merge, which routes the pull request **through the queue**. Merging via the Renovate API instead would either bypass the queue or be rejected by it. |
| `rebaseWhen` | `behind-base-branch` (via `:rebaseStalePrs`) | `conflicted` | The queue tests each pull request against trunk itself, so rebasing merely to catch up is wasted CI. Worse, pushing to a branch that is sitting in the queue **evicts it**. Only rebase on a real conflict. |

The `v:patch` label must exist in the repository.

The branch protection settings listed under [Repository Setup](#repository-setup) also change for these repositories: "Require branches to be up to date before merging" is replaced by the queue and should be off, and `Check Version` is replaced by the version label check.

## Links

- [Configuration Options](https://renovatebot.com/docs/configuration-options)
- [Renovate Presets](https://docs.renovatebot.com/config-presets/)

---

Released under the [APACHE 2](LICENSE).
