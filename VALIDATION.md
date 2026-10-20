# Validation performed

- Image-wrapper synthetic regression suite: **69 passed, 0 failed**.
- Python syntax compilation of `tools/imginfo`: passed.
- Bash syntax check of `tools/mkbootimg-dt`: passed.
- Inspected the selected kernel's actual `lineage-gtaxlwifi_defconfig`:
  gzip enabled; LZMA disabled; three EUR Wi-Fi DTBs selected; ext4/F2FS
  encryption enabled.
- Confirmed Wi-Fi DTB model string matches the DTBH packer filter.
- Full Android/TWRP build: **not performed**.
- Device boot, touch, mounts, MTP/ADB and decryption: **not tested**.

A missing size-warning message in the inherited helper was restored to make its
existing regression check pass. Over-limit diagnostics now recommend gzip-safe
changes rather than claiming LZMA is mandatory for this kernel.

## Separate vendor update

- Static checks confirm `/vendor` and `/vendor_image` use the same VENDOR
  by-name path and flags as the FlominatorGD reference tree.
- BoardConfig uses `TARGET_COPY_OUT_VENDOR := vendor`.
- No partition resize/create operation or guessed vendor capacity is included.
- Actual VENDOR block-device availability and kernel support remain untested.
