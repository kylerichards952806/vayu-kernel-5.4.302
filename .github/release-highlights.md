<!--
Hand-written highlights for the release notes. The workflow drops this file in
above the commit list, so what is written here appears at the top of every
release until it is changed. Update or empty it when the next build has
something different to say.
-->

### What's in this build

Ducky's `MI_SM8150_5.4` at the commit this build was made from, plus the root
layer and nothing else:

- **KernelSU-Next v3.4.0-legacy**, builtin, hooked through kprobes because this
  tree is non-GKI and has no vendor `security/` hooks to sit in.
- **SUSFS 2.3.0** on top of it, with the KernelSU-Next glue that lets the
  manager drive it.

Everything I had added on top of Ducky's kernel is gone from this build: the
fuel gauge work, the DS28E16 battery authentication, the thermal and panel
changes, the network defaults and the DTS edits. This is his kernel with root
and su hiding, so a problem that is not about KernelSU or SUSFS is his tree's
to answer — and one that is, is probably mine.
