# git_sample — portable history (why not a renamed `.git`)

This folder carries the **full commit history** of both repositories in two
transport-safe forms, so it can be uploaded / archived / shared even where a live
`.git` directory cannot. It is itself a git repository, so it can be pushed like
any other.

| artifact | what it is | use it when |
|---|---|---|
| `meat-proxy.git/`, `meat-proxy-en.git/` | **bare clones** — real git repositories with a non-dot name | you want the history browsable as ordinary files, or clonable directly |
| `meat-proxy.bundle`, `meat-proxy-en.bundle` | **bundles** — the whole history in one file | you want a single uploadable artifact |

Both forms **exclude local config and credentials by design** (a bundle carries
refs + objects only; a bare clone has no working-tree config), which is exactly
what makes them safe to share — unlike zipping a live `.git`.

## Why the live `.git` was NOT renamed

- `git push` never uploads `.git`; the server builds its own. So for GitHub the
  question is moot.
- Web-UI folder uploads skip / ignore dot-folders, and GitHub will not adopt an
  uploaded `.git` as history anyway.
- Renaming a live `.git` stops that directory being a repository at all
  (every git command fails) — breaking the sample to save the sample.
- A renamed `.git` is inert; a **bare clone** is the sanctioned "git directory with
  a safe name", and a **bundle** is the sanctioned single-file form.

## Restoring from this folder

```bash
# from a bundle (single file)
git clone meat-proxy.bundle meat-proxy
git clone meat-proxy-en.bundle meat-proxy-en

# or from the bare clones
git clone meat-proxy.git meat-proxy
git clone meat-proxy-en.git meat-proxy-en

# or attach a bundle as a remote of an existing clone and fetch
cd meat-proxy && git remote add sample ../git_sample/meat-proxy.bundle && git fetch sample
```

## Integrity

```
$ git bundle verify meat-proxy.bundle
The bundle records a complete history.
$ git bundle verify meat-proxy-en.bundle
The bundle records a complete history.
```

Restore test performed: cloning `meat-proxy.bundle` reproduces the full tree
(`src/ docs/ figures/ results/ tests/ …`) and both commits
(`4bfae7b` → `8d4e123`).

## Heads

- `meat-proxy`    : `8d4e123` scientific-completeness layer (review points 1-12)
- `meat-proxy-en` : `5a4f413` EN mirror of the scientific-completeness layer

## Note on recursion

This repository *contains* bundles/bare-clones of the other two repositories. If you
re-bundle `git_sample` itself, the nested history travels inside it; that is intended
for a sample, but do not treat `git_sample` as the source of truth — the working
repositories `../meat-proxy` and `../meat-proxy-en` are.
