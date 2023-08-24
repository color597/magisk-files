## v26.1-delta (31fbbbfc3)

- [General] Use MagiskHide/SuList to manage the visibility of modified files by module
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
- [Module] Simplify inject magisk bins

### Magisk upstream

- Sync upstream source code to official Magisk v26.1 (eddc862fa)

