# TWRP 12.1 device tree — Samsung Galaxy Tab A 10.1 (2016) Wi-Fi

**Device:** `gtaxlwifi` / **SM-T580**  
**Repository name:** `android_device_samsung_gtaxlwifi`  
**Branch:** `twrp-12.1`  
**Checkout path:** `device/samsung/gtaxlwifi`

## Status

Initial, unbuilt and hardware-untested source port. This is NOT a verified
flashable release. No recovery image is included. Do not use on SM-T585,
SM-P580, or other LTE/S-Pen variants.

Based on the supplied Exynos7870-labs LineageOS 20 device/common trees and
samsungexynos7870's a3y17lte TWRP 12.1 tree. See SOURCES.md for revisions.
No LineageOS common device tree or proprietary vendor checkout is required
by this recovery tree.

## Device-specific changes

- ARM64/Cortex-A53 recovery with ARM secondary architecture.
- SM-T580 identifiers and `twrp_gtaxlwifi-eng` lunch target.
- `lineage-gtaxlwifi_defconfig` from **Exynos7870-labs** kernel `lineage-20`.
  The supplied A3 kernel branch does not contain this defconfig; do not silently
  substitute it or the S-Pen `gtaxladwifi` defconfig.
- Gzip ramdisk: this device's kernel config enables RD_GZIP, not RD_LZMA.
- Samsung legacy boot header with a separately packed DTBH section, base
  `0x10000000`, page size 2048, and the tablet's Wi-Fi DTB model filter.
- Recovery partition capacity 39,845,888 bytes, from gtaxl-common.
- Legacy non-A/B `/system` with a separate **existing VENDOR partition**, following
  FlominatorGD's `android-9.0-vendor-twrp` tree. `TARGET_COPY_OUT_VENDOR := vendor`.
  `/vendor` supports filesystem backup/wipe; `/vendor_image` supports raw image
  backup/flashing. No RADIO/modem entry for this Wi-Fi model.
- EFS and HIDDEN/preload backup entries; microSD and USB OTG storage.
- Landscape HDPI theme for the 1920x1200 tablet display. Actual framebuffer
  orientation and brightness node must be confirmed on hardware.
- FBE policy v1 support requested to match the supplied LineageOS fstab.
  This does not establish working decryption, especially for stock Samsung FDE.
- No spoofed platform version/security patch or ALLOW_MISSING_DEPENDENCIES.

## Separate vendor partition prerequisite

This branch now targets a **custom vendor-partition layout**, not the original
LineageOS layout with vendor files inside `/system/vendor`.

Reference: https://github.com/FlominatorGD/android_device_samsung_gtaxlwifi/tree/android-9.0-vendor-twrp

The fstab uses `/dev/block/platform/13540000.dwmmc0/by-name/VENDOR` exactly as
that reference does. Adding fstab entries does **not** create a partition,
resize userdata, change the PIT, or install vendor contents. No VENDOR size is
guessed and no vendor image is built by this recovery-only tree. No HIDDEN or
CACHE alias is added. Existing HIDDEN/preload remains a separate entry; if your
custom layout removed or repurposed HIDDEN, adjust that entry to the actual PIT.

Before using vendor backup, wipe or image flashing, verify from a known-good
recovery that the VENDOR link resolves to the intended, distinct block device:

```sh
adb shell 'ls -l /dev/block/platform/13540000.dwmmc0/by-name/'
adb shell 'readlink -f /dev/block/platform/13540000.dwmmc0/by-name/VENDOR'
adb shell 'cat /proc/partitions'
```

These commands inspect the layout; they do not establish that the partition is
safe to format. Confirm its identity and size against your custom PIT/layout.
The selected LineageOS kernel's compatibility with this custom partition layout
has **not** been verified. If it does not expose VENDOR, the kernel/layout needs
additional work; do not simply point the entry at an arbitrary partition.

## Build

On a supported Linux Android build host, initialize a clean TWRP 12.1 workspace:

```sh
repo init -u https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp.git -b twrp-12.1
repo sync
```

Place this repository at `device/samsung/gtaxlwifi`. Fetch its kernel explicitly
(the dependency JSON is also included for tooling that supports it):

```sh
git clone -b lineage-20 --single-branch \
  https://github.com/Exynos7870-labs/android_kernel_samsung_exynos7870.git \
  kernel/samsung/exynos7870
source build/envsetup.sh
lunch twrp_gtaxlwifi-eng
mka recoveryimage
```

Expected output: `out/target/product/gtaxlwifi/recovery.img`.
The inherited kernel setup requests GCC, not clang, for this Linux 3.18 kernel.
The build environment must supply a compatible cross compiler; toolchain and
host-tool compatibility have not been validated in a full Android checkout.
Avoid adding hardware/samsung's DTBH modules alongside the vendored ones,
which would duplicate module names.

## Local packaging checks

```sh
bash tools/tests/test-mkbootimg-dt.sh
python3 tools/imginfo --help
# After a real build:
python3 tools/imginfo --max-size 39845888 \
  --expect-ramdisk-compression gzip \
  out/target/product/gtaxlwifi/recovery.img
```

The helper tests use synthetic images: they do NOT compile TWRP or prove that
Samsung's bootloader accepts the output. Images above 32 MiB receive a warning;
the 39,845,888-byte partition limit does not prove a bootloader loading limit.
If gzip cannot fit, reduce features first. Using LZMA requires explicitly enabling
CONFIG_RD_LZMA in the kernel, rebuilding it, and changing the compression check.

## Before flashing / hardware validation

Keep a known-good recovery and stock firmware available. Back up personal data
and EFS. Confirm the exact SM-T580 model, unlocked/custom-recovery-capable
bootloader, PIT layout and RECOVERY capacity before flashing anything.

Validate the built header, gzip ramdisk, and DTBH revision entries first.
Then test recovery boot, display orientation, touch, hardware keys, brightness,
ADB, MTP, microSD, OTG, and read-only filesystem mounts before any writes.
Check ext4/F2FS detection and encrypted data separately. Do not format `/data`
just because decryption fails. Decryption may require further keymaster/crypto
integration; none is claimed here. FBE flags target the supplied LineageOS setup,
not all stock/custom ROM encryption schemes. Never wipe EFS or preload casually.

## Publish to GitHub

Create an EMPTY repository named `android_device_samsung_gtaxlwifi` in the
intended account or organization. From the included Git bundle:

```sh
git clone android_device_samsung_gtaxlwifi-twrp-12.1.bundle android_device_samsung_gtaxlwifi
cd android_device_samsung_gtaxlwifi
git remote remove origin
git remote add origin https://github.com/YOUR_OWNER/android_device_samsung_gtaxlwifi.git
git push -u origin twrp-12.1
```

The archive contains files only. If using it instead, run `git init -b twrp-12.1`,
`git add .`, and `git commit` before adding the remote and pushing. Replace
YOUR_OWNER with the actual account or organization. No GitHub repository was
created or pushed by this workspace.

## Credits / licensing

Original contributors to LineageOS, TWRP, OrangeFox, TwrpBuilder and
samsungexynos7870. Original file copyright/license notices are retained;
this repository contains files under different licenses, not a blanket relicensing.
