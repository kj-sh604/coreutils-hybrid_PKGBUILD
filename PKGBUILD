# Maintainer: dr460nf1r3 <dr460nf1r3 at garudalinux dot org>
# Contributor: Sébastien "Seblu" Luttringer
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Allan McRae <allan@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>
# Freeloader:  kj_sh604 <kj@aedrielkylejavier.me>

pkgname=coreutils-hybrid
_pkgname=coreutils
__pkgname=uutils-coreutils
pkgver=9.4_0.0.23
_pkgver=9.4
__pkgver=0.0.23
pkgrel=604
pkgdesc='GNU coreutils / uutils-coreutils hybrid package. Uses stable uutils programs mixed with GNU counterparts if uutils counterpart is unfinished / buggy'
arch=('x86_64')
license=('GPL3' 'MIT')
url='https://www.gnu.org/software/coreutils/'
_url='https://github.com/uutils/coreutils'
depends=('glibc' 'acl' 'attr' 'gmp' 'libcap' 'openssl')
conflicts=('coreutils')
provides=('coreutils')
makedepends=('rust' 'cargo')
source=("https://ftp.gnu.org/gnu/$_pkgname/$_pkgname-$_pkgver.tar.xz"
        "$__pkgname-$__pkgver.tar.gz::$_url/archive/$__pkgver.tar.gz")
sha512sums=('a6ee2c549140b189e8c1b35e119d4289ec27244ec0ed9da0ac55202f365a7e33778b1dc7c4e64d1669599ff81a8297fe4f5adbcc8a3a2f75c919a43cd4b9bdfa'
            '6130c4d7ca8a31c95a15b0e5e64cc69ff21e4417fe118d04c354a30933379a99334c87416418ca29f5e31f25f689686d83329f971e970c1802aa5de933ad1207')

prepare() {
  cd $_pkgname-$_pkgver
  # apply patch from the source array (should be a pacman feature)
  local filename
  for filename in "${source[@]}"; do
    if [[ "$filename" =~ \.patch$ ]]; then
      echo "Applying patch ${filename##*/}"
      patch -p1 -N -i "$srcdir/${filename##*/}"
    fi
  done
  :
}

build() {
  # build gnu coreutils without the stable uutils programs counterparts leaving out: stat, touch, realpath (genfstab broken)
  # kj-sh604: added the following: date, tail, ls, ln | removed the following: tr (for password generation in pass)
  cd $_pkgname-$_pkgver
  ./configure \
      --prefix=/usr \
      --libexecdir=/usr/lib \
      --with-openssl \
      --enable-no-install-program="groups,hostname,kill,uptime,arch,base32,base64,basename,cat,chgrp,chmod,chown,chroot,cksum,comm,csplit,cut,date,dircolors,dirname,du,env,echo,expand,factor,false,fmt,fold,groups,head,hostid,hostname,id,kill,ls,link,ln,logname,mkdir,mkfifo,mknod,mktemp,mv,nice,nl,nohup,nproc,paste,pathk,pinky,printenv,ptx,pwd,readlink,relpath,rm,rmdir,seq,shred,shuf,sleep,stdbuf,sort,sum,sync,tac,tee,tail,timeout,true,truncate,tsort,tty,uname,unexpand,uniq,unlink,uptime,users,who,wc,whoami,yes"
}

package() {
  # install uutils-coreutils, skip the buggy parts
  cd $_pkgname-$__pkgver
  make \
      DESTDIR="$pkgdir" \
      PREFIX=/usr \
      MANDIR=/share/man/man1 \
      PROG_PREFIX= \
      install

  # install gnu coreutils over the uutils-coreutils
  cd $srcdir && cd $_pkgname-$_pkgver
  make DESTDIR="$pkgdir" install
  
  # clean conflicts, arch ships these in other apps
  cd $pkgdir/usr/bin
  rm groups hostname kill more uptime

  rm $pkgdir/usr/share/bash-completion/completions/*
}
