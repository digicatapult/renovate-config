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

## Links

- [Configuration Options](https://renovatebot.com/docs/configuration-options)
- [Renovate Presets](https://docs.renovatebot.com/config-presets/)

---

Released under the [APACHE 2](LICENSE).
