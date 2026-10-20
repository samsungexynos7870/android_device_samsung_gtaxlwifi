# TWRP for the Samsung Galaxy Tab A 10.1 (2016) Wi-Fi

TWRP device tree for the Samsung Galaxy Tab A 10.1 (2016) Wi-Fi (`gtaxlwifi`,
SM-T580), based on the TWRP AOSP 12.1 manifest. The Exynos 7870 kernel must be
built from its `twrp-12.1` branch so the tablet defconfig and DTBs match.
This tree is not for SM-T585 or the SM-P580/S-Pen variants. Only SM-T580.

## Build

```bash
mkdir twrp && cd twrp
repo init --depth=1 \
  -u https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp.git \
  -b twrp-12.1

mkdir -p .repo/local_manifests
cat > .repo/local_manifests/gtaxlwifi.xml <<'EOF_MANIFEST'
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
    <remote fetch="https://github.com/samsungexynos7870" name="exynos7870-labs" />
    <project name="android_device_samsung_gtaxlwifi" path="device/samsung/gtaxlwifi"
             remote="exynos7870-labs" revision="twrp-12.1" />
    <project name="android_kernel_samsung_exynos7870" path="kernel/samsung/exynos7870"
             remote="exynos7870-labs" revision="twrp-12.1" />
</manifest>
EOF_MANIFEST

repo sync
source build/envsetup.sh
lunch twrp_gtaxlwifi-eng
mka recoveryimage
```

The recovery image is `out/target/product/gtaxlwifi/recovery.img`.

## DTBH image handling

Samsung Exynos 7870 boot images use a DTBH table appended after the kernel and
ramdisk. `build/tasks/dtb.mk` builds the tablet kernel DTBs and packages them
with the vendored LineageOS `dtbhtool`; `tools/mkbootimg-dt` appends the DTBH
image and checks the recovery size and LZMA ramdisk before the image is accepted.

`dtbhtool/` is based on the LineageOS `android_hardware_samsung` DTBH tooling
(branch `lineage-19.1`). The SM-T580 Wi-Fi filter and identifiers are in
`dtbhtool/include/samsung_dtbh.h`. The image-size guard uses the configured
RECOVERY capacity (39,845,888 bytes); it checks partition fit, not any separate
sboot limit.

## Credits
2020 @Astrako
2026 samsungexynos7870

`dtbhtool/` is the DTBH tooling of the LineageOS project
(`LineageOS/android_hardware_samsung`, branch `lineage-19.1`), vendored
unchanged.

## Contact
Telegram support group: https://t.me/joinchat/D1Jk_VbieGBXOWZt2y8O7A
