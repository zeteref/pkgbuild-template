# Maintainer: Your Name <you@example.com>

pkgname=yourproject
pkgver=0.1.0
pkgrel=1
pkgdesc="Short one-line description"
arch=('any')                     # use 'x86_64' if you ship a compiled binary
url="https://github.com/you/yourproject"
license=('MIT')
depends=()                       # runtime deps, e.g. ('electron' 'bash')
makedepends=()                   # build deps, e.g. ('rust' 'pkgconf')
options=()                       # e.g. ('!debug') for cargo

# This PKGBUILD is SELF-CONTAINED: paru clones THIS repo and runs makepkg
# inside it, so the "sources" are the repo files themselves. There is no
# source=() and no git clone of itself — that would be circular.
#
# "$startdir" is the repo root (absolute). Always reference it, never a bare
# relative path (makepkg runs package() with its cwd switched to $srcdir).

# Write the built package to /tmp instead of cluttering the repo dir.
PKGDEST=/tmp

# Only needed if your project compiles (e.g. Rust). Uncomment and adapt:
# build() {
#   cargo build --release --locked --manifest-path "$startdir/Cargo.toml" --target-dir "$srcdir/target"
# }

package() {
  # ---- A) Plain script / prebuilt executable ----
  install -Dm755 "$startdir/yourproject" "$pkgdir/usr/bin/yourproject"

  # ---- B) Compiled binary (with the build() step above) ----
  # install -Dm755 "$srcdir/target/release/yourproject" "$pkgdir/usr/bin/yourproject"

  # ---- C) Electron-style app (dir of files) ----
  # install -d "$pkgdir/usr/lib/yourproject"
  # cp -r "$startdir/main.js" "$startdir/preload.js" "$startdir/renderer" "$startdir/package.json" \
  #   "$pkgdir/usr/lib/yourproject/"
  # install -Dm755 "$startdir/yourproject" "$pkgdir/usr/bin/yourproject"

  # ---- Common extras ----
  # install -Dm644 "$startdir/config.example" "$pkgdir/usr/share/yourproject/config.example"
  # install -Dm644 "$startdir/LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}
