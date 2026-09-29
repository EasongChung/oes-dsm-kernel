# oes-dsm-kernel

OES (OneThing Cloud, Amlogic A311D) DSM 5.10.55 kernel port.

## Why
The stock ported kernel is not reproducible and its AHCI/PCIe behaviour on
meson is unverified. This tree rebuilds the DSM kernel from the public
`931122/rk_syno_kernel` source with clean, auditable OES-specific code.

## Content
- `oes-meson-patch.diff` - all OES changes vs upstream (8 files)
- release asset `rk_syno_kernel-with-oes.tar.gz` - full tree, patch applied
- `.github/workflows/build-oes-meson.yml` - CI build

## Build
```bash
export ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-
make O=out meson_a311d_oes_dsm_defconfig
make O=out -j"$(nproc)" Image dtbs
make O=out -j"$(nproc)" modules
```

## Identity
`sn=` / `mac1=` on the kernel cmdline. No SN/MAC is hardcoded in the kernel:
`drivers/mtd/devices/oes_fake_spi.c` reads `gszSerialNum` / `grgbLanMac`
(populated by `kernel/syno_bootargs.c`) and seeds the vendor MTD partition in
the OEM layout that `drivers/mtd/mtdpart.c:syno_vender_v1_parser()` reads.
