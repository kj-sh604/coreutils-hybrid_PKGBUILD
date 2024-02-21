pkgname=coreutils-hybrid
pkgver=9.4_0.0.24
pkgrel=604
pkgdesc='GNU coreutils / uutils-coreutils hybrid package. Uses stable uutils programs mixed with GNU counterparts if uutils counterpart is unfinished / buggy'
gnu_coreutils=coreutils
rust_uutils=uutils-coreutils
gnu_coreutils_version=9.4
rust_uutils_version=0.0.24
arch=('x86_64')
license=('GPL3' 'MIT')
url='https://www.gnu.org/software/coreutils/'
_url='https://github.com/uutils/coreutils'
depends=('glibc' 'acl' 'attr' 'gmp' 'libcap' 'openssl')
conflicts=('coreutils')
provides=('coreutils')
makedepends=('rust' 'cargo')
source=("https://ftp.gnu.org/gnu/$gnu_coreutils/$gnu_coreutils-$gnu_coreutils_version.tar.xz"
        "$rust_uutils-$rust_uutils_version.tar.gz::$_url/archive/$rust_uutils_version.tar.gz")
sha512sums=('a6ee2c549140b189e8c1b35e119d4289ec27244ec0ed9da0ac55202f365a7e33778b1dc7c4e64d1669599ff81a8297fe4f5adbcc8a3a2f75c919a43cd4b9bdfa'
            '6130c4d7ca8a31c95a15b0e5e64cc69ff21e4417fe118d04c354a30933379a99334c87416418ca29f5e31f25f689686d83329f971e970c1802aa5de933ad1207')

prepare() {
  cd $gnu_coreutils-$gnu_coreutils_version
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
  # build gnu coreutils without the stable uutils programs counterparts
  cd $gnu_coreutils-$gnu_coreutils_version
  ./configure \
      --prefix=/usr \
      --libexecdir=/usr/lib \
      --with-openssl \
      --enable-no-install-program="groups,hostname,kill,uptime,arch,base32,base64,basename,cat,chgrp,chmod,chown,chroot,cksum,comm,csplit,cut,date,dircolors,dirname,du,env,echo,expand,factor,false,fmt,fold,groups,head,hostid,hostname,id,kill,ls,link,ln,logname,mkdir,mkfifo,mknod,mktemp,mv,nice,nl,nohup,nproc,paste,pathk,pinky,printenv,printf,ptx,pwd,readlink,relpath,rm,rmdir,seq,shred,shuf,sleep,stdbuf,sort,sum,sync,tac,tee,tail,timeout,tr,true,truncate,tsort,tty,uname,unexpand,uniq,unlink,uptime,users,who,wc,whoami,yes"
}

package() {
  # install uutils-coreutils, skip the buggy parts
  cd $gnu_coreutils-$rust_uutils_version
  make \
      DESTDIR="$pkgdir" \
      PREFIX=/usr \
      MANDIR=/share/man/man1 \
      PROG_PREFIX= \
      install

  # install gnu coreutils over the uutils-coreutils
  cd $srcdir && cd $gnu_coreutils-$gnu_coreutils_version
  make DESTDIR="$pkgdir" install
  
  # clean conflicts, arch ships these in other apps
  cd $pkgdir/usr/bin
  rm groups hostname kill more uptime
  rm $pkgdir/usr/share/bash-completion/completions/*
  rm $pkgdir/usr/share/man/man1/{groups.1,hostname.1,kill.1,more.1,uptime.1}
}
