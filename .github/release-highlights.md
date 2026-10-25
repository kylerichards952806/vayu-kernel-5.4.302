<!--
Hand-written highlights for the release notes. The workflow drops this file in
above the commit list, so what is written here appears at the top of every
release until it is changed. Update or empty it when the next build has
something different to say.
-->

### What's in this build

A short one, because the tree got shorter. It now tracks Ducky's 5.4 SM8150
branch with three things on top: the battery statistics fix, KernelSU-Next
3.4.2-legacy and SUSFS 2.3.0. Everything else I had been stacking — the DS28E16
battery authentication work, the charger thermal tables, the panel refresh-rate
floor, BBR and fq_codel defaults, the small scheduler and NPU tweaks — went back
to Ducky's version. A kernel that quietly differs from his in twenty places is
harder to trust than one that matches it.

What stayed is the battery statistics fix, because it works. The fuel gauge was
writing a partial SDAM cookie, so the learned capacity and the cycle count were
thrown away on every restart and Settings showed "Unavailable" for design and
maximum capacity. It writes the whole cookie now and keeps the learned capacity
when the battery profile has no nominal value. Design capacity, maximum capacity
and cycle count are real numbers, and they survive a reboot.

Under that: KernelSU-Next 3.4.2-legacy hooked through kprobes, and SUSFS 2.3.0
providing path, mount, kstat and cmdline hiding through the supercall. If you run
the SUSFS module, `ksu_susfs` is the interface it expects.

No DS28E16 and no 33 W work in this build. Ducky's tree leaves that option off
and so does this one, so a genuine pack and an aftermarket one both behave the way
they do on his kernel.

If something misbehaves, say which tag you are on (`uname -r`) and attach a
`dmesg` from the boot that went wrong. That is worth more than a description.

All credit for the base goes to [Ducky](https://github.com/DUCKS-QCM-5-4/android_kernel_xiaomi_sm8150).
This fork is only adaptations on top of his project, and the fixes that remain here
are mine. Thank you, Ducky.
