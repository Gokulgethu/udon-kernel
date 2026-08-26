# udon-kernel — OnePlus 11R cloud builds on GitHub Actions

Builds the **OnePlus 11R (udon)** kernel directly on **free GitHub Actions runners** —
no build farm, no crave, no local PC. The exact same kernel source your RisingOS
`sixteen-qpr2` ROM uses, pinned to the same commits:

| Piece | Source | Commit |
|---|---|---|
| Kernel | [rocko5498/android_kernel_oneplus_sm8450](https://github.com/rocko5498/android_kernel_oneplus_sm8450) | `7669ea3e` |
| Ext. modules | [pjgowtham/android_kernel_oneplus_sm8450-modules](https://github.com/pjgowtham/android_kernel_oneplus_sm8450-modules) | `ca110451` |
| Config | `gki_defconfig` + `vendor/waipio_GKI.config` + `vendor/oplus_GKI.config` + `vendor/debugfs.config` | — |
| Toolchain | AOSP `clang-r536225` (Android 16 era) with `LLVM=1 LLVM_IAS=1` | — |

## What you get

- **`udon-kernel-AnyKernel3.zip`** — flashable kernel zip (Image only, everything else in
  your current boot image is preserved). Attached to each build's GitHub Release and to
  the workflow artifacts.
- **`.config`** — the exact merged kernel configuration.
- **`module-out/`** — the out-of-tree Qualcomm modules (audio/camera/datarmnet/display/
  video/wlan...), built best-effort as artifacts. The ROM ships these inside
  `vendor_dlkm`/`vendor_boot` images, so they're reference material, not a flashable part.

Because the config and source are identical to the ROM's, the kernel ABI matches the
modules already inside your RisingOS build — an Image-only flash is the standard way
custom kernels ship for this device.

## Run a build

Actions tab → **Build udon kernel** → **Run workflow** → (optionally change the kernel
revision) → Run. Takes roughly 30–60 min on the free 4-vCPU runner. When it finishes,
grab the zip from the **Releases** page or the run's artifacts.

## Flash

```bash
adb reboot recovery        # or boot to Lineage/Rising recovery
adb sideload udon-kernel-AnyKernel3.zip
```

Reboot. Bootloader must be unlocked (it already is if you're running RisingOS/Lineage).

## Notes

- Free runners have a 6-hour job cap — this build fits comfortably.
- The kernel's device-tree sources live in a separate repo
  (`android_kernel_oneplus_sm8450-devicetrees`); DTBs/DTBOs are **not** replaced by this
  zip (the ROM's existing ones are reused, as intended).
- To build a different revision: run the workflow with a different `kernel_revision`
  input (branch name, tag, or commit SHA from the kernel repo).
