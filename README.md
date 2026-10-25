# vayu on 5.4 — Ducky's tree, with KernelSU-Next and SUSFS

Ducky's SM8150 5.4 port for the POCO X3 Pro (vayu, M2102J20SG), tracking his
branch, with one addition on top and nothing else:

- **KernelSU-Next** — `drivers/kernelsu`, v3.4.0-legacy, builtin, hooked through
  kprobes on this non-GKI tree.
- **SUSFS 2.3.0** — su hiding on top of it, with the KernelSU-Next glue.

Kernel release: `5.4.302-vayu-kyler084-0.0.x`.

Upstream: <https://github.com/DUCKS-QCM-5-4/android_kernel_xiaomi_sm8150>,
branch `MI_SM8150_5.4`. Everything clever in here is Ducky's; the SUSFS and
KernelSU-Next integration is simonpunk's and the KernelSU-Next project's work,
applied to his tree.

## Building

`.github/workflows/build-kernel.yml` builds `vayu` (arm64) with the Android
prebuilt clang 22 the vendor used, and publishes `boot.img`, `dtbo.img` and a
TWRP-flashable zip as a GitHub release. It refuses to build a branch that is
behind Ducky's tip; `.github/workflows/sync-upstream.yml` merges his branch
daily so it cannot drift.
