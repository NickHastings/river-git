
# Maintainer: Andrea Feletto <andrea@andreafeletto.com>
# Contributor: Daurnimator <daurnimator@archlinux.org>

pkgname=river-git
_pkgname=${pkgname%-*}
pkgrel=1
pkgdesc='A dynamic tiling wayland compositor.'
arch=('x86_64')
url='https://github.com/riverwm/river'
license=('GPL3')
depends=(
	'mesa' 'wlroots' 'wayland' 'wayland-protocols' 'libxkbcommon'
	'libevdev' 'pixman' 'xorg-xwayland'
)
optdepends=(
	'polkit: access seat through systemd-logind'
)
makedepends=('zig' 'git' 'scdoc')
provides=('river' 'riverctl' 'rivertile')
conflicts=('river' 'river-bin' 'river-noxwayland-git')
options=('!strip')
source=(
	"git+$url"
	'git+https://github.com/ifreund/zig-pixman.git'
	'git+https://github.com/ifreund/zig-wayland.git'
	'git+https://github.com/swaywm/zig-wlroots.git'
	'git+https://github.com/ifreund/zig-xkbcommon.git'
	'river.desktop'
)
sha256sums=(
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'SKIP'
	'6ccc55e95666904cbdeeeeed841a16f728cbae2609646130a4c53785e588e4b0'
)

prepare() {
	cd "$srcdir/$_pkgname"
	git submodule init
	for dep in pixman wayland wlroots xkbcommon; do
		git config "submodule.deps/zig-$dep.url" "$srcdir/zig-$dep"
	done
	git submodule update
}

pkgver() {
        # Set the pkgver variable to be the same as what "river
        # -version" will return. There is one caveat: pkgver string
        # "is not allowed to contain colons, forward slashes, hyphens
        # or whitespace" (man 5 PKGBUILD). Unreleased river versions
        # contain the string "-dev", so substitute and "-" for "_"

        local git_describe_long version commit_count commit_hash

        # Extract version from line like "0.2.0-dev" from build.zig
        # and replace - with _ (man 5 PKGBUILD says hyphens are not
        # permitted).
        version=$(sed -n 's/^const version = "\(.*\)";/\1/p' build.zig | tr '-' '_')

        # Get the commit count and hash from git desribe
        git_describe_long="$(git describe --long)"
        IFS='-' read -ra gdl_array <<< "$git_describe_long"
        commit_count=${gdl_array[1]}
        commit_hash=${gdl_array[2]}

        # Construct the correct version number (being sure to
        # pop the "g" off the front of the commit_hash
        pkgver="${version}.${commit_count}+${commit_hash:1}"
        echo "$pkgver"
}

package() {
	cd "$srcdir/$_pkgname"
	DESTDIR="$pkgdir" zig build --prefix '/usr' -Dxwayland
	install -Dm644 LICENSE -t "$pkgdir/usr/share/licenses/$_pkgname"
	install -Dm644 README.md -t "$pkgdir/usr/share/doc/$_pkgname"
	install -d "$pkgdir/usr/share/$_pkgname"
	cp -fR example "$pkgdir/usr/share/$_pkgname"

	cd "$srcdir"
	install -Dm644 river.desktop -t "$pkgdir/usr/share/wayland-sessions"
}
