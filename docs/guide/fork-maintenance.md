# Maintaining a Personal Fork

This guide is for maintainers who run their own fork of Oh My OpenCode while still tracking updates from the main project.

The recommended setup is:

- `origin` points to your fork
- `upstream` points to the main project
- `dev` is your integration branch
- feature work happens on short-lived branches off `dev`

## Remote Layout

Verify your remotes:

```bash
git remote -v
```

Expected layout:

```bash
origin   https://github.com/<you>/<your-fork>.git
upstream https://github.com/code-yeongyu/oh-my-openagent.git
```

If you cloned the main project first, add your fork and keep the original repository as `upstream`:

```bash
git remote rename origin upstream
git remote add origin https://github.com/<you>/<your-fork>.git
```

## Daily Workflow

Start new work from your fork's `dev` branch:

```bash
git checkout dev
git pull origin dev
git checkout -b <your-name>/<feature-name>
```

After you finish a change:

```bash
git add -A
git commit -m "feat: describe your change"
git push -u origin <your-name>/<feature-name>
```

Open a pull request from your feature branch into `dev` on your own fork.

Avoid pushing directly to `dev` except for simple sync commits from `upstream`.

## Syncing From Upstream

Pull changes from the main project into your fork regularly:

```bash
git fetch upstream
git checkout dev
git merge upstream/dev
git push origin dev
```

If the upstream project changes its default working branch, replace `dev` with the branch they actively maintain.

If you prefer a linear history, use `git rebase upstream/dev` instead of `git merge upstream/dev`.

## Working With Other Maintainers

If you want other people to help maintain your fork, use your fork as the shared repository. Do not ask them to push to `upstream`.

Recommended setup:

1. Add them as collaborators on your GitHub repository.
2. Ask them to clone your fork, not the original project.
3. Ask them to branch from `dev` and open pull requests back into your `dev`.
4. Review and merge into `dev`.
5. Periodically sync `dev` with `upstream/dev`.

### Add Collaborators

In GitHub:

1. Open your repository.
2. Go to `Settings`.
3. Open `Collaborators` or `Access`.
4. Invite people by GitHub username.

After they accept, they can push branches to your repository if your branch protection rules allow it.

### Branch Protection

For a shared fork, protect `dev`.

Recommended GitHub settings for `dev`:

- Require a pull request before merging
- Require at least 1 approval
- Dismiss stale approvals when new commits are pushed
- Require status checks to pass before merging
- Include administrators if you want the rules to apply to yourself too

This repository already has a `CI` workflow that runs on `dev`. In branch protection, require the checks that correspond to:

- `test`
- `typecheck`
- `build`

If you want a very tight maintainer model, also restrict direct pushes to `dev` to a small maintainer set.

## Optional: CODEOWNERS

If your fork has stable maintainers, add a `.github/CODEOWNERS` file so GitHub auto-requests reviews from the right people.

Example:

```text
* @your-github-name
src/agents/ @your-github-name @teammate
src/tools/ @your-github-name @teammate
docs/ @your-github-name
```

Only enable required code owner review if those owners are active and can review quickly.

## Upstream Contributions

Your fork can have two parallel goals:

- maintain your own custom version
- contribute selected improvements back upstream

When a change is useful to the main project, open a pull request from your fork to the upstream repository. Keep those PRs small and avoid bundling fork-only customizations with general improvements.

## Recommended Policy

For most maintainers, this policy works well:

- `upstream/dev` is the source of truth for the main project
- `origin/dev` is the integration branch for your fork
- all feature work lands through pull requests
- only reviewed code reaches `dev`
- sync from `upstream` on a regular schedule

That gives you a stable custom fork without losing the ability to absorb upstream changes.
