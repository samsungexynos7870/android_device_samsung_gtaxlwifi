# Source provenance

- `a3y17lte`: `44ca4762acf9ca1dc28dc94b352ab14a189905d2`
- `gtaxlwifi`: `56d41a133c85b1a37ef0b057c308f77fe136f46e`
- `gtaxl-common`: `2c893936b51e6010abd50c93962f7577a2b898cf`
- Kernel inspected: Exynos7870-labs/android_kernel_samsung_exynos7870, lineage-20, `b8027e71ae0ca89719bb9a5fdc23956a9daaf2b4`.

Original copyright and license notices are retained.
DTBH tooling and image wrapper come from the supplied A3 TWRP tree;
partition layout and DTBH platform/subtype codes come from gtaxl-common.
The device DTBH model filter is adapted to the gtaxlwifi DTB model strings.

## Separate vendor layout update

- Reference: https://github.com/FlominatorGD/android_device_samsung_gtaxlwifi/tree/android-9.0-vendor-twrp
- Inspected commit: `4a926dd0a0595ac7b2d238e54881725b22fbc148`.
- Adapted the VENDOR fstab entries and `TARGET_COPY_OUT_VENDOR := vendor`.
- Did not import unrelated modem partitions, old crypto settings, old boot-image
  tooling, or the reference kernel configuration.
