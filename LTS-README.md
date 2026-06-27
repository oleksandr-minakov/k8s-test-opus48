# Mirantis LTS fork — k8s-test-opus48

This is the **source** repo of a two-repo Mirantis LTS cascade for six
Kubernetes core components (`kube-apiserver`, `kube-controller-manager`,
`kube-scheduler`, `kube-proxy`, `kubelet`, `kubectl`), forked from
`kubernetes/kubernetes` and based on **`v1.32.13`**.

| Repo | Role |
|---|---|
| `oleksandr-minakov/k8s-test-opus48` (this) | Fork + LTS scaffolding; cuts `vX.Y.Z-lts.N` tags. |
| `oleksandr-minakov/control-repo-test-opus48` | Build orchestration; builds & publishes per release. |

Both pair on branch **`release-1.32`**.

## LTS files

| File | Purpose |
|---|---|
| `version.txt` | Current LTS version, `X.Y.Z-lts.N` (no `v`). release-please bumps it. |
| `CHANGELOG.md` | Auto-generated release notes (a real file — replaces upstream's symlink). |
| `.github/workflows/release-please.yaml` | Cuts the tag/Release; opens the cross-repo VERSION bump PR. |
| `.github/workflows/lts-tests.yml` | Per-PR vendored unit + compile checks for the six components. |

## Release flow

1. Backport a fix (e.g. a CVE) onto `release-1.32` via a `feat:`/`fix:` PR.
   `lts-tests` must be green.
2. Trigger **Release Please** (Actions UI / dispatch). It opens a Release PR
   bumping `version.txt` + `CHANGELOG.md`.
3. Merge it → release-please cuts `vX.Y.Z-lts.N` + a GitHub Release, then opens
   a VERSION bump PR in the build repo (if `BUILD_REPO_TOKEN` is set).
4. Merge the build-repo PR → `build.yaml` builds & publishes the LTS artifacts.

See [LTS-BRANCHING.md](./LTS-BRANCHING.md) for versioning rules and how to
force the next `-lts.N`.
