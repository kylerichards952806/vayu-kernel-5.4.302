# vayu on 5.4 — my personal tweak branch

Kernel I run on my own POCO X3 Pro (vayu, M2102J20SG) on LineageOS 24.0.

Important bit first: this is **not** my port. Getting a 5.4 kernel booting on
SM8150 was Ducky's work. I picked his tree up because I wanted a couple of things
working properly on my phone, and I've been fixing and adjusting from there. Treat
anything clever in here as his, and anything broken as mine.

Ducky's tree: <https://github.com/DUCKS-QCM-5-4/android_kernel_xiaomi_sm8150>
Branch used here: `MI_SM8150_5.4`. Kernel release: `5.4.302-vayu-kyler084-0.0.x`.

## Thanks

- **Ducky** (duckyduck@metonator.dev, DUCKS-QCM-5-4) for the 5.4 SM8150 bring-up,
  the thermal work, and for keeping it public so the rest of us can even try this.
  Everything that works here starts with his tree.
- The **KernelSU-Next** people for v3.4.2-legacy and for the 5.4 compat shims that
  make it buildable on a vendor kernel at all.
- **simonpunk** for SUSFS, and everyone who keeps it building on older kernels.
- **LineageOS** for the ROM this is built against.
- Anyone who flashes this and actually tells me what broke.

## What is different from Ducky's tree

Deliberately short. Three things, and nothing else:

- **Battery stats survive a reboot.** `qpnp-fg-gen4` writes the whole SDAM cookie
  instead of a partial one, so the learned capacity and cycle count aren't thrown
  away on restart, and it keeps the learned capacity when the battery profile has
  no nominal value. Design capacity, maximum capacity and cycle count are real
  numbers in Settings now, and they stay put across reboots.
- **KernelSU-Next 3.4.2-legacy, built in.** Hooked through kprobes, because the
  syscall-table mode can never bootstrap the manager on this ROM. Includes the 5.4
  header shims and `path_umount()` that a vendor 5.4 tree needs.
- **SUSFS 2.3.0, built in.** Path, mount, kstat and cmdline hiding, wired to
  KernelSU's supercall. `ksu_susfs` is the binary the SUSFS module talks to.

That is the whole list. Everything I had been piling on top — the DS28E16 battery
authentication work, the chargerpolicy and thermal table edits, BBR/fq_codel
defaults, the panel refresh-rate floor, the small scheduler and NPU tweaks — has
been pulled back out to Ducky's version. It made the tree drift away from his, and
a kernel that behaves unpredictably is worth less than one that matches upstream.

- **CI does the boring parts.** Every push builds with Android clang 22, splices
  this tree's own DTB into `boot.img`, verifies the packaged images against that
  DTB, refuses to build if the branch is behind Ducky's upstream, and publishes a
  release with `boot.img`, `dtbo.img`, a TWRP zip, the exact config, checksums and
  a changelog of what changed since the previous release. Ducky's merged commits
  are listed separately in the notes, because they are his work.
- **Notes.** `AUDIT.md` has the long version — diagnostics, the boot-image
  detective work, the KernelSU history. It's more detail than anyone needs, but
  it's there so I don't re-learn the same things twice.

## Things that are not in here

- **Battery authentication (DS28E16) and anything 33 W.** Ducky's tree leaves
  `CONFIG_BATT_VERIFY_BY_DS28E16` off, and so does this one now. If your pack is an
  aftermarket one it will behave exactly as it does on his kernel.
- **Play Integrity.** An unlocked bootloader plus a custom kernel means the ROM
  side needs PIF, Tricky Store and a keybox. None of that lives in here.

## Flashing

Grab `boot.img` and the TWRP zip from [Releases](../../releases). With fastboot:

```sh
fastboot flash boot boot.img
fastboot flash dtbo dtbo.img
```

Or flash the zip in TWRP; it writes both partitions and leaves a copy of the
previous pair on `/sdcard` so you can get back.

This is built for and tested on LineageOS 24.0 only. Don't flash it on MIUI or on a
different Android version and then wonder why it doesn't boot.

## Building

Push to the branch and the GitHub Actions workflow does it: toolchain download,
build, image splice, verification, release. If you want it locally, use the same
clang the workflow pins; a random distro clang will build something, but it won't
be what the releases are.

## Reporting a problem

Boot loop, dead display, a battery value that looks wrong — say which release tag
you're on (`uname -r`) and what the phone did. A `dmesg` from the failed boot is
worth more than a description of it.
