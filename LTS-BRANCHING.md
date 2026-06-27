# LTS branching & versioning

## Tag format

Flat-version tags only: **`vX.Y.Z-lts.N`** (e.g. `v1.32.13-lts.0`).

- `X.Y.Z` tracks the upstream patch we forked from (`v1.32.13`).
- `-lts.N` is the Mirantis LTS counter, incremented per LTS release.
- No component prefix in the tag — component identity lives in the **image
  registry path** (`ghcr.io/oleksandr-minakov/lts-k8s/<component>`), not the tag.
  (Component-prefixed tags require release-please manifest mode, which has open
  upstream bugs.)

`version.txt` holds the bare version (`1.32.13-lts.0`); the build repo's
`VERSION` holds the `v`-prefixed tag (`v1.32.13-lts.0`).

## Branch layout

One release branch per LTS line, mirrored across both repos:

```
source  k8s-test-opus48      release-1.32   ── vX.Y.Z-lts.N tags
build   control-repo-...     release-1.32   ── VERSION pin
```

A second line (e.g. 1.33) is a new `release-1.33` pair, seeded from its upstream
tag with its own bootstrap `vX.Y.Z-lts.0` tag. No workflow changes needed — the
triggers already match `release-*`.

## Bootstrap tag (one-time per line)

release-please needs a previous-release anchor or it fails with
`found 0 possible releases`:

```bash
SHA=$(git rev-parse release-1.32)
git tag v1.32.13-lts.0 "$SHA" && git push origin v1.32.13-lts.0
gh release create v1.32.13-lts.0 --target release-1.32 \
  --title v1.32.13-lts.0 --notes "Bootstrap LTS release equivalent to upstream v1.32.13."
```

## Forcing the next `-lts.N`

`release-type: simple` follows conventional-commit semver by default, so a
`feat:` would bump the minor instead of the LTS counter. To pin the exact next
LTS version, add a footer to the backport commit (release-please honours it):

```
fix: backport CVE-2025-xxxxx

Release-As: 1.32.13-lts.1
```

Then run **Release Please**; the Release PR will propose `v1.32.13-lts.1`.
