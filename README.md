# PKGBUILD project template

A starting point for a personal project installable with `paru` via its
"pkgbuild repositories" feature — the same way `flip` is installed.

## Files

- `PKGBUILD` — self-contained packaging (no `source=`, packages the repo's own files).
- `justfile` — `just bump` / `just release` to bump the version and commit.
- `.gitignore` — ignores makepkg/build artifacts.

## 1. Customize the PKGBUILD

Fill in the placeholders (`pkgname`, `pkgver`, `pkgdesc`, `url`, `depends`)
and the `package()` body. See the comments in the PKGBUILD for the common
patterns: plain script, Rust binary (with `build()`), and Electron-style app.

## 2. Make it a git repo and push

```sh
git init
git add -A
git commit -m "init"
git remote add origin git@github.com:you/yourproject.git
git push -u origin master
```

## 3. Register it with paru (on each machine)

Add a section to `~/.config/paru/paru.conf`:

```ini
[yourproject]
Url = https://github.com/you/yourproject.git
Depth = 1
SkipReview
GenerateSrcinfo
```

(`GenerateSrcinfo` means you don't need to commit a `.SRCINFO`.)

## 4. Install / update

```sh
# first time on a machine:
paru -Sy --pkgbuilds      # download (clone) the pkgbuild repo
paru -S yourproject       # install

# after code changes:
just release              # bump version (patch) + commit
git push
# on the other machine:
paru -Syu                 # picks up the new pkgver
```

## Notes

- **Self-contained:** paru clones the repo and runs `makepkg` inside it, so the
  "sources" are the repo files themselves. `$startdir` points at the clone root;
  always reference files via `$startdir/...`, never a bare relative path.
- **`PKGDEST=/tmp`** keeps built `.pkg.tar.zst` files out of the repo.
- **Versioning:** with a static `pkgver`, other machines only see an update when
  you bump it (via `just release`) and push — that's the intended flow.
