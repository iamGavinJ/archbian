# AArch64 multi-platform
# Taken from: https://raw.githubusercontent.com/archlinuxarm/PKGBUILDs/master/core/linux-aarch64/PKGBUILD

pkgbase=linux-meson64
_kernelname=${pkgbase#linux}
_desc="AArch64 multi-platform for Meson SoC"
pkgver=5.19.16
pkgrel=1
arch=('aarch64')
url="http://www.kernel.org/"
license=('GPL2')
options=('!strip')
source=("http://apt.armbian.com/pool/main/l/linux-${pkgver}${_kernelname}/linux-image-edge-meson64_22.08.6_arm64.deb"
        "http://apt.armbian.com/pool/main/l/linux-${pkgver}${_kernelname}/linux-dtb-edge-meson64_22.08.6_arm64.deb"
        "http://apt.armbian.com/pool/main/l/linux-${pkgver}${_kernelname}/linux-headers-edge-meson64_22.08.6_arm64.deb"
        'linux.preset'
        '60-linux.hook'
        '90-linux.hook')
md5sums=('01613865b564f107f21ea0c82469ebf2'
         '9c335fb78c7f877c783603460b3126d6'
         'b57b02c2a62a179d97fb7b82964a6d9d'
         '41cb5fef62715ead2dd109dbea8413d6'
         '0a5f16bfec6ad982a2f6782724cca8ba'
         '3dc88030a8f2f5a5f97266d99b149f77')
noextract=('linux-image-edge-meson64_22.08.6_arm64.deb'
           'linux-dtb-edge-meson64_22.08.6_arm64.deb'
           'linux-headers-edge-meson64_22.08.6_arm64.deb')

prepare() {
    for _file in ${noextract[@]}; do
        # _thisfile="${_file%-edge-meson64_22.08.6_arm64.deb}"
        case ${_file%-edge-meson64_22.08.6_arm64.deb} in
            "linux-image")
                bsdtar -xf ${_file} 'data.tar.xz'
                tar -xf 'data.tar.xz' --xform='s#lib/modules/#usr/lib/modules/#' './boot' './lib'
                # mv "boot/vmlinuz-${pkgver}${_kernelname}" "boot/Image"
                rm -R "lib/modules"
                ;;
            "linux-dtb")
                bsdtar -xf ${_file} 'data.tar.xz'
                tar -xf 'data.tar.xz' --xform='s#/dtb-.*-meson64/#/dtbs/#' './boot'
                rm -R "boot/dtb-${pkgver}${_kernelname}"
                ;;
            "linux-headers")
                echo headers
                bsdtar -xf ${_file} 'data.tar.xz'
                ;;
        esac
    done

    rm 'data.tar.xz'
}

# build() {
# }

_package() {
  
  pkgdesc="The Linux Kernel and modules - ${_desc}"
  depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
  optdepends=('wireless-regdb: to set the correct wireless channels of your country')
  provides=("linux=${pkgver}" "WIREGUARD-MODULE")
  conflicts=('linux')
  backup=("etc/mkinitcpio.d/${pkgbase}.preset")
  install=${pkgname}.install

  local kernver="${pkgver}${_kernelname}"
  cp -at "${pkgdir}" "boot" "usr"
  ln -rs "${pkgdir}/boot/vmlinuz-${pkgver}${_kernelname}" "${pkgdir}/boot/Image"

  # sed expression for following substitutions
  local _subst="
    s|%PKGBASE%|${pkgbase}|g
    s|%KERNVER%|${kernver}|g
  "

  # install pacman script
  sed "${_subst}" "${startdir}/linux.install" \
    > "${startdir}/${pkgbase}.install"

  # install mkinitcpio preset file
  sed "${_subst}" "${startdir}/linux.preset" |
    install -Dm644 /dev/stdin "${pkgdir}/etc/mkinitcpio.d/${pkgbase}.preset"

  # install pacman hooks
  sed "${_subst}" "${startdir}/60-linux.hook" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/60-${pkgbase}.hook"
  sed "${_subst}" "${startdir}/90-linux.hook" |
    install -Dm644 /dev/stdin "${pkgdir}/usr/share/libalpm/hooks/90-${pkgbase}.hook"
}

_package-headers() {
  false
#   pkgdesc="Header files and scripts for building modules for linux kernel - ${_desc}"
#   provides=("linux-headers=${pkgver}")
#   conflicts=('linux-headers')

#   cd $_srcname
#   local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

#   echo "Installing build files..."
#   install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
#     localversion.* version vmlinux
#   install -Dt "$builddir/kernel" -m644 kernel/Makefile
#   install -Dt "$builddir/arch/arm64" -m644 arch/arm64/Makefile
#   cp -t "$builddir" -a scripts

#   # add xfs and shmem for aufs building
#   mkdir -p "$builddir"/{fs/xfs,mm}

#   echo "Installing headers..."
#   cp -t "$builddir" -a include
#   cp -t "$builddir/arch/arm64" -a arch/arm64/include
#   install -Dt "$builddir/arch/arm64/kernel" -m644 arch/arm64/kernel/asm-offsets.s
#   mkdir -p "$builddir/arch/arm"
#   cp -t "$builddir/arch/arm" -a arch/arm/include

#   install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
#   install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

#   # https://bugs.archlinux.org/task/13146
#   install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

#   # https://bugs.archlinux.org/task/20402
#   install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
#   install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
#   install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

#   # https://bugs.archlinux.org/task/71392
#   install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

#   echo "Installing KConfig files..."
#   find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

#   echo "Removing unneeded architectures..."
#   local arch
#   for arch in "$builddir"/arch/*/; do
#     [[ $arch = */arm64/ || $arch == */arm/ ]] && continue
#     echo "Removing $(basename "$arch")"
#     rm -r "$arch"
#   done

#   echo "Removing documentation..."
#   rm -r "$builddir/Documentation"

#   echo "Removing broken symlinks..."
#   find -L "$builddir" -type l -printf 'Removing %P\n' -delete

#   echo "Removing loose objects..."
#   find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

#   echo "Stripping build tools..."
#   local file
#   while read -rd '' file; do
#     case "$(file -bi "$file")" in
#       application/x-sharedlib\;*)      # Libraries (.so)
#         strip -v $STRIP_SHARED "$file" ;;
#       application/x-archive\;*)        # Libraries (.a)
#         strip -v $STRIP_STATIC "$file" ;;
#       application/x-executable\;*)     # Binaries
#         strip -v $STRIP_BINARIES "$file" ;;
#       application/x-pie-executable\;*) # Relocatable binaries
#         strip -v $STRIP_SHARED "$file" ;;
#     esac
#   done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

#   echo "Adding symlink..."
#   mkdir -p "$pkgdir/usr/src"
#   ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}



pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in ${pkgname[@]}; do
  eval "package_${_p}() {
    _package${_p#${pkgbase}}
  }"
done