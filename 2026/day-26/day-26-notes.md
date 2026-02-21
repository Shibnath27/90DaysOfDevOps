# 📄 `day-26-notes.md`

---

# Day 26 – GitHub CLI (gh)

---

# 🔹 Task 1: Install & Authenticate

## Install

Linux:

```bash
sudo apt install gh
```

macOS:

```bash
brew install gh
```

Verify:

```bash
gh --version
```

---

## Authenticate

```bash
gh auth login
```

Verify logged-in account:

```bash
gh auth status
```

---

## Authentication Methods Supported

* HTTPS (browser-based OAuth)
* Personal Access Token (PAT)
* SSH authentication
* GitHub Enterprise support

For automation:
👉 PAT or environment variables are commonly used.

---

# 🔹 Task 2: Working with Repositories

---

## Create Repo from Terminal

```bash
gh repo create devops-gh-practice --public --source=. --remote=origin --push
```

Or create with README:

```bash
gh repo create test-repo --public --add-readme
```

---

## Clone Using gh

```bash
gh repo clone username/repo-name
```

---

## View Repo Details

```bash
gh repo view
```

With web:

```bash
gh repo view --web
```

---

## List All Repositories

```bash
gh repo list
```

Limit results:

```bash
gh repo list --limit 20
```

---

## Delete Repo (Careful)

```bash
gh repo delete repo-name --confirm
```

---

# 🔹 Task 3: Issues

---

## Create Issue

```bash
gh issue create --title "Bug in login flow" \
--body "Login API returns 500 under load" \
--label bug
```

---

## List Issues

```bash
gh issue list
```

---

## View Specific Issue

```bash
gh issue view 3
```

---

## Close Issue

```bash
gh issue close 3
```

---

## Automation Use Case

You can:

* Auto-create issues from CI failures
* Generate issues from monitoring alerts
* Parse JSON output using:

```bash
gh issue list --json title,number
```

This enables scripting in DevOps pipelines.

---

# 🔹 Task 4: Pull Requests

---

## Create Branch & Push

```bash
git switch -c feature-cli-test
echo "CLI test" >> test.txt
git add .
git commit -m "Add CLI test file"
git push -u origin feature-cli-test
```

---

## Create PR from Terminal

```bash
gh pr create --fill
```

Or specify:

```bash
gh pr create --base main --head feature-cli-test --title "Add CLI test" --body "Testing GitHub CLI workflow"
```

---

## List PRs

```bash
gh pr list
```

---

## View PR Details

```bash
gh pr view 5
```

With checks:

```bash
gh pr view 5 --web
```

---

## Merge PR

```bash
gh pr merge 5 --merge
```

Other merge methods:

```bash
--squash
--rebase
```

---

## Merge Methods Supported

* Merge commit
* Squash merge
* Rebase merge

---

## Review Someone Else’s PR

```bash
gh pr checkout 5
gh pr review 5 --approve
gh pr review 5 --comment -b "Looks good overall."
```

---

# 🔹 Task 5: GitHub Actions Preview

---

## List Workflow Runs

```bash
gh run list
```

---

## View Run Details

```bash
gh run view <run-id>
```

---

## CI/CD Use Case

* Monitor pipeline status
* Trigger workflows
* Extract job results for automation
* Fail deployments based on run status

Machine-readable output:

```bash
gh run list --json status,conclusion
```

---

# 🔹 Task 6: Useful gh Commands

---

## Raw API Calls

```bash
gh api repos/owner/repo/issues
```

Useful for automation and scripting.

---

## Create Gist

```bash
gh gist create file.txt --public
```

---

## Create Release

```bash
gh release create v1.0.0 --title "First Release" --notes "Initial version"
```

---

## Create Alias

```bash
gh alias set prlist 'pr list'
```

Now:

```bash
gh prlist
```

---

## Search Repositories

```bash
gh search repos devops --limit 10
```

---

# 📄 Update `git-commands.md`

Add:

---

## 🔹 GitHub CLI (gh)

```bash
gh auth login
gh auth status
gh repo create
gh repo list
gh repo clone
gh issue create
gh issue list
gh issue close
gh pr create
gh pr list
gh pr merge
gh run list
gh api
```

