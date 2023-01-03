# Maintainer: Pellegrino Prevete <pellegrinoprevete@gmail.com>

# shellcheck disable=SC2034
_module="ee"
_platform="ps2"
target="mips64r5900el-${_platform}-elf"
_base="toolchain"
pkgname="${target}-gcc"
pkgver="v10.2.0"
# pkgver="v11.3.0"
_islver="0.21"
pkgrel=1
_pkgdesc=("The GNU Compiler Collection (${target}).")
pkgdesc="${_pkgdesc[*]}"
arch=('x86_64')
license=('BSD')
_github="https://github.com/ps2dev"
_local="ssh://git@127.0.0.1:/home/git"
url="${_github}/gcc"
makedepends=("${target}-binutils"
             "${target}-gcc-stage1"
             "${target}-newlib"
             "${target}-newlib-nano"
             "${target}-pthread-embedded"
             "libgmp-static"
             "mpfr-static"
             "libmpc-static"
             "zstd-static")
optdepends=()
_branch="${_module}-${pkgver}"
_commit="331453616ac96717cfef82d21c03573c8984f17d"
source=("${target}-gcc::git+${_github}/gcc#commit=${_commit}"
        "https://libisl.sourceforge.io/isl-${_islver}.tar.xz")
# source=("${pkgbase}-gcc::git+${_local}/${_platform}-gcc#commit=${_branch}")
sha256sums=('SKIP'
            'SKIP')
            # '6d6c1aa00e2a6dfc509fa46d9a9dbe93af0c451e196a670577a148feecf6b8a5')

_n_cpu="$(getconf _NPROCESSORS_ONLN)"
_make_opts=(-j "${_n_cpu}")

cflags=(-static
        -Wno-implicit-function-declaration)

ldflags=(${LDFLAGS}
         -Bstatic
         -s)

prepare() {
  cd "${srcdir}/${pkgname}" || exit
  rm "isl" || true
  ln -s "../isl-${_islver}" "isl"
}

# shellcheck disable=SC2154
build() {
  local _target

  CFLAGS=""
  CXXFLAGS=""
  CPPFLAGS=""
  LDFLAGS=""
  export CFLAGS
  export CXXFLAGS
  export CPPFLAGS
  export LDFLAGS
  export PATH="${PATH}:/usr/${target}/bin"

  local _cflags=(${cflags[@]}
                 -I/usr/include
                 # -I/usr/include/isl
                 -L"/usr/lib"
                 # -I"/usr/${target}/include"
                 # -L"/usr/${target}/lib"
                 # -I"/usr/${target}/include/pthread-embedded"
                 # -include "/usr/${target}/include/pthread-embedded/pthread.h"
                 # -I"/usr/${target}/include/pthread-embedded/bits"
                 # -I"/usr/${target}/include/newlib-nano"
                 # -I"/usr/${target}/include/newlib-nano"
                 # -std=c99
                 # -std=c++98
                 # -nostdinc
                 -O2)

  local _ldflags=(${ldflags[@]}
                  # -L"/usr/${target}/lib"
                  # -L"/usr/${target}/lib/pthread-embedded"
                  # -lstdin
                  -l:libisl.a
                  # -ldl
                  -lpthread)
                  # -llibisl)
                  # -L"/usr/${target}/lib/newlib-nano")

  local _build_opts=(${_make_opts[@]}
                     CFLAGS="${_cflags[*]}"
                     CPPFLAGS="${_cflags[*]}"
                     CXXFLAGS="${_cflags[*]}"
                     LDFLAGS="${_ldflags[*]}")

  cd "${srcdir}/${pkgname}"

  for _target in "${target}"; do
    rm -rf "build-${_target}"
    mkdir -p "build-${_target}"
    cd "build-${_target}"
    local _configure_opts=(--prefix="/usr"
                           --program-prefix=${_target}-
                           --with-local-prefix=/usr/${_target}
                           --with-sysroot=/usr/${_target}
                           --with-build-sysroot=/usr/${_target}
                           --target="${_target}"
                           --host=${CHOST}
                           --build=${CHOST}
                           --enable-languages="c,c++"
                           --disable-bootstrap
                           --disable-shared
                           --enable-static
                           --with-float=hard
                           --with-sysroot="/usr/${_target}"
                           --with-newlib
                           # --disable-graphite
                           --with-isl
                           # --with-osl
                           --disable-libssp
                           --disable-multilib
                           --enable-cxx-flags=-G0
                           --enable-threads=posix
                           --disable-tls)

    # CC="/usr/bin/${target}-gcc" \
    # CPP="/usr/bin/${target}-cpp" \
    # CXX="/usr/bin/${target}-g++" \
    # CC="/usr/bin/gcc-11" \
    # CPP="/usr/bin/cpp-11" \
    # CXX="/usr/bin/g++-11" \
    CC="/usr/bin/gcc-7" \
    CPP="/usr/bin/cpp-7" \
    CXX="/usr/bin/g++-7" \
    "../configure" ${_configure_opts[@]}

    # CC="/usr/bin/${target}-gcc" \
    # CPP="/usr/bin/${target}-cpp" \
    # CXX="/usr/bin/${target}-g++" \
    # CC="/usr/bin/gcc-11" \
    # CPP="/usr/bin/cpp-11" \
    # CXX="/usr/bin/g++-11" \
    make "${_build_opts[@]}" all
    
    cd ..
  done
}

# shellcheck disable=SC2154
package() {
  local _target
  cd "${srcdir}/${pkgname}"
  for _target in "${target}"; do
    cd "build-${_target}"
    make DESTDIR="${pkgdir}" "${_make_opts[@]}" install-strip
    cd ..
  done
}
