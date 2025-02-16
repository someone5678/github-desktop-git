# Maintainer: someone5678 <someone5678 dot dev at google dot com>

pkgname=github-desktop-git
_pkgname="github-desktop"
pkgver=3.4.17
_suffix=-beta3
_gitname="release-$pkgver$_suffix"
pkgrel=3
pkgdesc='GUI for managing Git and GitHub (unofficial Linux support patch applied official upstream)'
arch=(x86_64)
url='https://desktop.github.com'
license=(MIT)
provides=('github-desktop')
conflicts=('github-desktop')
depends=(curl
         git
         libsecret
         libxss
         nspr
         nss
         org.freedesktop.secrets
         unzip)
optdepends=('github-cli: CLI interface for GitHub'
            'hub: CLI interface for GitHub')
makedepends=(imagemagick
             python
             python-setuptools
             nodejs
             npm
             xorg-server-xvfb
             yarn)
DLAGENTS=("http::/usr/bin/git clone --branch $_gitname --single-branch %u")
source=("$pkgname::git+https://github.com/desktop/desktop.git#tag=$_gitname"
        'git+https://github.com/github/gemoji.git'
        'git+https://github.com/github/gitignore.git'
        'git+https://github.com/github/choosealicense.com.git'
        "$_pkgname.desktop"
        "github"
        # Patches from https://github.com/shiftkey/desktop
        '0000-app_src_cli_main.ts.patch'
        '0000-app_src_main-process_app-window.ts.patch'
        '0000-app_src_main-process_main.ts.patch'
        '0000-app_src_ui_about_about.tsx.patch'
        '0000-app_src_ui_add-repository_add-existing-repository.tsx.patch'
        '0000-app_src_ui_app.tsx.patch'
        '0000-app_src_ui_window_title-bar.tsx.patch'
        '0000-app_src_ui_window_window-controls.tsx.patch'
        '0000-app_styles_ui_window__title-bar.scss.patch'
        '0000-script_dist-info.ts.patch'
        )
sha256sums=('SKIP'
            'SKIP'
            'SKIP'
            'SKIP'
            '2f2b5425658106c080c17bf57af4541630157aa2e1133b3033bb6e24a95dd179'
            '6d59c9cf63f3aa86bec9ec6b225899cc84bf651e00d112a7d55026e35d9c7dcf'
            # Patches from https://github.com/shiftkey/desktop
            'd0d9d82c163b538185a449d5304271414b3ffdd897e00059f06854d4bc4b8e2e'
            '9bfe0224dc8f3ce0630303085bd538f5466a772d0b156a3af9bea98c552e1a33'
            '2e0dc550765b5e58a0e0f6efc3a025c638aba27b3185c91fb80c0aeaf86a6ba6'
            '25c4d7b2191b05d181cb8b58519b6b0c918ca50d0cebe72ced90be807fc92eba'
            'eb3e9732120b0c04e4b907b7be9252f75e3e7845fd67a6d371d1183cb2b90e40'
            'f22893cd5385d1d2bec5bf55f6b31b32592823515dbf255b5cbfb3b151437d2e'
            '71afc34f3b38bbf1283e837a01d5eda5ccd8a8258c41eada79ad2be0b74c9d32'
            '6a107ec5dcbaae526c5be03956fb0d04e633639f3b513b89759c2d0f670a887c'
            'b397cc80473212632eb3d823559d78d2e55dc6bac283fc7bcb82ab78ed2fa515'
            '855c1225fd8f15aa558ed7810761859418025655ba688d5f0020c892c1162822'
            )

prepare() {
    cd "$pkgname"
    git submodule init
    git config submodule."gemoji".url "$srcdir/gemoji"
    git config submodule."app/static/common/gitignore".url "$srcdir/gitignore"
    git config submodule."app/static/common/choosealicense.com".url "$srcdir/choosealicense.com"
    git -c protocol.file.allow=always submodule update
    # https://github.com/shiftkey/desktop/issues/809#issuecomment-1348815685
    sed -e '/compile:prod/s/4096/4096 --openssl-legacy-provider/' -i package.json
    for size in 32 64 128; do
        magick app/static/logos/1024x1024.png -resize $size app/static/logos/${size}x${size}.png
    done
    cp -rf --preserve=mode app/static/logos app/static/linux/logos
    mkdir -p app/static/linux
    # https://github.com/shiftkey/desktop
    for patch in $srcdir/*.patch; do
        git apply $patch
    done
}

build() {
    cd "$pkgname"
    # https://github.com/nodejs/node/issues/48444
    export UV_USE_IO_URING=0
    xvfb-run yarn install --ignore-engines
    xvfb-run yarn add --ignore-engines xml2js xvfb-maybe yaml @types/xml2js patch-package postinstall-postinstall
    xvfb-run yarn build:prod
}

package() {
    cd "$pkgname"
    install -d "$pkgdir/opt/$_pkgname"
    cp -r --preserve=mode dist/desktop-linux-x64/* "$pkgdir/opt/$_pkgname/"
    install -Dm0644 "$startdir/$_pkgname.desktop" "$pkgdir/usr/share/applications/$_pkgname.desktop"
    install -Dm0755 "$startdir/github" "$pkgdir/usr/bin/github"
    pushd "$pkgdir/opt/$_pkgname/resources/app/static/logos"
    install -Dm0644 "1024x1024.png" "$pkgdir/usr/share/icons/hicolor/1024x1024/apps/$_pkgname.png"
    install -Dm0644 "512x512.png" "$pkgdir/usr/share/icons/hicolor/512x512/apps/$_pkgname.png"
    install -Dm0644 "256x256.png" "$pkgdir/usr/share/icons/hicolor/256x256/apps/$_pkgname.png"
    install -Dm0644 "128x128.png" "$pkgdir/usr/share/icons/hicolor/128x128/apps/$_pkgname.png"
    install -Dm0644 "64x64.png" "$pkgdir/usr/share/icons/hicolor/64x64/apps/$_pkgname.png"
    install -Dm0644 "32x32.png" "$pkgdir/usr/share/icons/hicolor/32x32/apps/$_pkgname.png"
}
