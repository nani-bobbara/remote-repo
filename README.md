# 🗂️ Central Staging Registry & Remote Repo Sync Engine

This repository is a staging area for pushing code into other GitHub repositories.
Drop a zip file in, and a GitHub Actions pipeline validates the filename, unpacks
it, and delivers it to the target repo — opening a pull request if that repo
already exists, or creating the repo and pushing straight to `main` if it doesn't.

---

## 📦 Uploading Code

Upload a zip file to the root of this repository, named:

    <target-repo-name>.<version>.zip

- **`<target-repo-name>`** — the exact name of the GitHub repository you want the
  code delivered to.
- **`<version>`** — a version marker for this upload (e.g. `001`, `002`). It shows
  up in the branch name and PR title for traceability, but isn't validated for
  sequence — any label works as long as the filename is unique.

Example:

```text
Remote-Repo (this staging registry)
├── README.md
├── .github/workflows/nested-unzip-and-pr.yml
├── demo-one.001.zip          <-- delivers to "demo-one", tagged version 001
├── demo-one.002.zip          <-- a later update to "demo-one"
└── mini-ecom-bot.001.zip     <-- "mini-ecom-bot" doesn't exist yet -> created
```

## 🚀 What Happens Next

1. **Validate.** The filename must match `<target-repo-name>.<version>.zip`.
   A non-matching file is left alone.
2. **Strict repo lookup.** The pipeline checks for a GitHub repo with the
   exact target name.
   - **Repo exists** → a feature branch (`sync-<version>`) is created, the
     zip is unpacked onto it, and a pull request is opened into `main`.
   - **Repo doesn't exist** → it's created as a **private** repository, the
     zip is unpacked, and the code is pushed straight to `main` (a brand-new
     repo has nothing to protect, so no branch/PR is needed).
3. **Cleanup.** Once delivery succeeds either way, the zip file is
   automatically deleted from this staging registry.
