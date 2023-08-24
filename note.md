## d2a66567-delta Nightly build

- Do not use local socket path
- Fix root loss after zygote restarted with "Unmount modules by default" enabled

Canary and Debug are built from the same source code.  Debug builds have more detailed logs and are suitable for debugging. Canary builds have less logs, are more stable than Debug, and are suitable for most common uses

If you like my work, you can donate me at [PayPal/HuskyDG](http://paypal.me/huskydg)

### Diffs to official Magisk

- [General] Use "Unmount modules" to manage the visibility of modified files by module
- [Manager] Support installing into system partition for emulator
- [General] Copy required files to `/system` for `addon.d`
- [Manager] Show all supported languages in Language settings for Chinese ROM
- [Module] Support systemless deleting files or folders for modules
- [General] Built-in Bootloop Protection to protect system from bootloop by magisk module
- [General] Tune F2FS driver to fix problem on unencrypted f2fs `/data`
- [MagiskInit] Support Pre-Init mount that replaces system files before `init` starts
- [MagiskInit] Support loading custom rc script from pre-init directory
- [Modules] Support magic mount more partitions (`my_*`, `odm`, `optics`, `prism`)
- [Zygisk]: Switch to use native bridge by 5ec1cff
- [Module] Live patch `sepolicy.rule` if it is not found in `sepolicy.rules` directory
- [MagiskSU] Do not use local socket path
- [Module] Always inject magisk bins
- [MagiskInit] Introduce `early-mount.d/v2` to support pre-Init mount per module

### Magisk upstream

- Sync upstream source code to 350d0d600

