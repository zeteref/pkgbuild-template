# PKGBUILD project template

A starting point for a personal project installable with `paru` via its
"pkgbuild repositories" feature — the same way `flip` is installed.

## Files

- `PKGBUILD` — self-contained packaging (no `source=`, packages the repo's own files).
- `justfile` — `just bump` / `just release` to bump the version and commit.
- `.gitignore` — ignores makepkg/build artifacts.

## The model (why the PKGBUILD looks the way it does)

The package is **self-contained**: paru clones *this* repo and runs `makepkg`
inside it, so the "sources" are the repo files themselves. There is no
`source=()` and no git-clone-of-itself (that would be circular).

Because of that:

- Always reference files via **`$startdir/...`** (the absolute repo/clone root),
  never a bare relative path — `makepkg` runs `package()` with its working
  directory switched to `$srcdir`, so `cp main.js` would look in the wrong place.
- **`PKGDEST=/tmp`** sends the built `.pkg.tar.zst` to `/tmp` instead of
  cluttering the repo. `-i` still installs from there.

## Customizing `package()`

Fill `pkgname`, `pkgver`, `pkgdesc`, `url`, `depends` and the `package()` body.
Common patterns:

**Plain script / prebuilt executable** (the default):
```bash
install -Dm755 "$startdir/yourproject" "$pkgdir/usr/bin/yourproject"
```

**Rust binary** (add `makedepends=('rust' 'pkgconf')`, `arch=('x86_64')`, and a `build()`):
```bash
build() {
  cargo build --release --locked --manifest-path "$startdir/Cargo.toml" --target-dir "$srcdir/target"
}

package() {
  install -Dm755 "$srcdir/target/release/yourproject" "$pkgdir/usr/bin/yourproject"
}
```

**Electron-style app** (a directory of files + a launcher):
```bash
package() {
  install -d "$pkgdir/usr/lib/yourproject"
  cp -r "$startdir/main.js" "$startdir/preload.js" "$startdir/renderer" "$startdir/package.json" \
    "$pkgdir/usr/lib/yourproject/"
  install -Dm755 "$startdir/yourproject" "$pkgdir/usr/bin/yourproject"
}
```

Extras you'll often want: `install -Dm644 "$startdir/LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"`,
and any `config.example` → `usr/share/$pkgname/`.

## 1. Make it a git repo and push

```sh
git init && git add -A && git commit -m "init"
git remote add origin git@github.com:you/yourproject.git
git push -u origin master
```

## 2. Register it with paru (on each machine)

Add a section to `~/.config/paru/paru.conf`:

```ini
[yourproject]
Url = https://github.com/you/yourproject.git
Depth = 1
SkipReview
GenerateSrcinfo
```

(`GenerateSrcinfo` means you don't need to commit a `.SRCINFO`.)

## 3. Install / update

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

- **Versioning:** with a static `pkgver`, other machines only see an update when
  you bump it (via `just release`) and push — that's the intended flow.
- `just release` does a *patch* bump (`0.1.0 → 0.1.1`); use `just release minor`
  or `just release major` for bigger jumps.
