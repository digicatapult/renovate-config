# Renovate Config

## Usage

### Organisation setup

Ensure renovatebot is added with [access to all repositories](https://github.com/apps/renovate)

Ensure renovate approver is added with [access to all repositories](https://github.com/apps/renovate-approve)

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

## Links

- [Configuration Options](https://renovatebot.com/docs/configuration-options)
- [Renovate Presets](https://docs.renovatebot.com/config-presets/)

---

Released under the [APACHE 2](LICENSE).
