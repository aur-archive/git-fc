# Maintainer: Felipe Contreras <felipe.contreras@gmail.com>

pkgname=git-fc
pkgver=2.0.0+fc2
pkgrel=1
pkgdesc='A fork of Git with more features'
arch=(i686 x86_64)
url='http://git-scm.com/'
license=('GPL2')
depends=('curl' 'expat>=2.0' 'perl-error' 'perl>=5.14.0' 'openssl' 'pcre')
makedepends=('python2' 'emacs' 'libgnome-keyring' 'xmlto' 'asciidoc')
optdepends=('tk: gitk and git gui'
            'perl-libwww: git svn'
            'perl-term-readkey: git svn'
            'perl-mime-tools: git send-email'
            'perl-net-smtp-ssl: git send-email TLS support'
            'perl-authen-sasl: git send-email TLS support'
            'python2: various helper scripts'
            'subversion: git svn'
            'cvsps: git cvsimport'
	    'mercurial: support for Mercurial repositories'
            'bzr: support for Bazaar repositories'
            'gnome-keyring: GNOME keyring credential helper')
replaces=('git-core')
conflicts=('git')
provides=('git-core' 'git')
install=git-fc.install
source=("https://github.com/felipec/git/archive/v${pkgver}.tar.gz"
        git-fc-daemon@.service
        git-fc-daemon.socket)

build() {
  export PYTHON_PATH='/usr/bin/python2'
  cd "$srcdir/git-${pkgver/+/-}"
  make prefix=/usr gitexecdir=/usr/lib/git-core \
    CFLAGS="$CFLAGS" LDFLAGS="$LDFLAGS" \
    USE_LIBPCRE=1 \
    NO_CROSS_DIRECTORY_HARDLINKS=1 \
    MAN_BOLD_LITERAL=1 \
    all doc

  make -C contrib/emacs prefix=/usr
  make -C contrib/credential/gnome-keyring
}

check() {
  export PYTHON_PATH='/usr/bin/python2'
  cd "$srcdir/git-${pkgver/+/-}"
  local jobs
  jobs=$(expr "$MAKEFLAGS" : '.*\(-j[0-9]*\).*')
  mkdir -p /dev/shm/git-test
  make prefix=/usr gitexecdir=/usr/lib/git-core \
    CFLAGS="$CFLAGS" LDFLAGS="$LDFLAGS" \
    USE_LIBPCRE=1 \
    NO_CROSS_DIRECTORY_HARDLINKS=1 \
    MAN_BOLD_LITERAL=1 \
    NO_SVN_TESTS=y \
    DEFAULT_TEST_TARGET=prove \
    GIT_PROVE_OPTS="$jobs -Q" \
    GIT_TEST_OPTS="--root=/dev/shm/git-test" \
    test
}

package() {
  export PYTHON_PATH='/usr/bin/python2'
  cd "$srcdir/git-${pkgver/+/-}"
  make prefix=/usr gitexecdir=/usr/lib/git-core \
    CFLAGS="$CFLAGS" LDFLAGS="$LDFLAGS" \
    USE_LIBPCRE=1 \
    NO_CROSS_DIRECTORY_HARDLINKS=1 \
    MAN_BOLD_LITERAL=1 \
    INSTALLDIRS=vendor DESTDIR="$pkgdir" install install-doc

  # emacs
  make -C contrib/emacs prefix=/usr DESTDIR="$pkgdir" install
  # gnome credentials helper
  install -m755 contrib/credential/gnome-keyring/git-credential-gnome-keyring \
      "$pkgdir"/usr/lib/git-core/git-credential-gnome-keyring
  make -C contrib/credential/gnome-keyring clean
  # the rest of the contrib stuff
  mkdir -p "$pkgdir"/usr/share/git/
  cp -a ./contrib/* "$pkgdir"/usr/share/git/

  # remove perllocal.pod, .packlist, and empty directories.
  rm -rf "$pkgdir"/usr/lib/perl5

  # git-daemon via systemd socket activation
  install -D -m 644 "$srcdir"/git-fc-daemon@.service "$pkgdir"/usr/lib/systemd/system/git-fc-daemon@.service
  install -D -m 644 "$srcdir"/git-fc-daemon.socket "$pkgdir"/usr/lib/systemd/system/git-fc-daemon.socket
}

md5sums=('2666a4492645cd28b22bb737f28c2f41'
         '042524f942785772d7bd52a1f02fe5ae'
         'f67869315c2cc112e076f0c73f248002')
