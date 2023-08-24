# Magisk Delta Internal Documentation

This document explains some of the advanced features of Magisk Delta, a systemless root solution for Android devices. Magisk Delta allows you to modify your device without altering the original boot image, and provides a unified interface for managing modules, root access, and more.

## Early mount

Some files need to be mounted as early as possible in the boot process, before the `init` process is executed. Magisk Delta provides a way to mount files in the `pre-init` stage, using the `early-mount.d` directory.

- To check if Magisk Delta supports `early-mount.d/v2`, use this code:

```
EARLYMOUNTV2=false
if grep "$(magisk --path)/.magisk/early-mount.d" /proc/mounts | grep -q '^early-mount.d/v2'; then
    EARLYMOUNTV2=true
fi
```

- To find the `early-mount.d` directory (Magisk v26+), use this code:

```
$(magisk --path)/.magisk/early-mount.d
```

### General

- The general early-mount partition will be mounted at `$MAGISKTMP/.magisk/early-mount.d` due to the new sepolicy rules implementation in Magisk Delta v26.0+ with `early-mount.d/v2`. The early-mount partition is the same as the preinit partition, which could be one of these: `data`, `cache`, `metadata`, `cust`, `persist`, etc. Magisk will hardcode the preinit partition while patching the boot image.

> (*) Be careful when using `persist` or `metadata` for early-mount, as these partitions have very limited size. Filling them up might cause the device to fail to boot.

- You can place your files into the corresponding location under the `early-mount.d` directory. For example, if you want to replace `/vendor/etc/vintf/manifest.xml`, copy your `manifest.xml` to `$MAGISKTMP/.magisk/early-mount.d/system/vendor/etc/vintf/manifest.xml`. Magisk Delta will mount your files in the next reboot. Other files that are not in `early-mount.d/system` will be ignored.

### Module

- Since Magisk Delta v26.0+ with `early-mount.d/v2`, you can also place your early-mount files in `/data/adb/modules/<module_id>/early-mount`.

**Note: Early-init mount only supports simple mount, which means it can replace files but cannot add new files, folders or replace folders**

## init.rc inject

### General

If you want to inject custom `*.rc` scripts into your device without repacking the boot image, Magisk Delta provides a way to do that systemlessly. You can use the `initrc.d` directory to store your custom scripts, and Magisk Delta will inject them into `init.rc` every boot.

- The location of custom `*.rc` scripts is `$PRENITDIR/early-mount.d/initrc.d`. Magisk Delta will inject all scripts in this folder into `init.rc` every boot.
- You can use `${MAGISKTMP}` to refer to Magisk's tmpfs directory. Every occurrence of the pattern `${MAGISKTMP}` in your `*.rc` scripts will be replaced with the Magisk tmpfs directory when magiskinit injects it into `init.rc`.
- Magisk's mirror will not be available while booting, so you need to use this pattern `${MAGISKTMP}/.magisk/early-mount.d` to access the copy of the `early-mount.d` directory.

- Here is an example of a custom script named `custom.rc` that you can use with `initrc.d`:

```
# Use ${MAGISKTMP} to refer to Magisk's tmpfs directory

on early-init
    setprop sys.example.foo bar
    insmod ${MAGISKTMP}/.magisk/early-mount.d/libfoo.ko
    start myservice

service myservice ${MAGISKTMP}/.magisk/early-mount.d/myscript.sh
    oneshot
```

### Module

- Since Magisk Delta v26.0+ with `early-mount.d/v2`, you can also place your initrc.d files in `/data/adb/modules/<module_id>/early-mount/initrc.d`.

## Remove files and folders

- Using Magisk module is the easy way to modify system partitions without actually making changes to the system partitions, and the changes can be easily. Magisk modules can replace and add any file or folder to system. However, removing files by Magisk modules is still not allowed.

- In Magisk documentation:

> It is complicated to actually remove a file (possible, not worth the effort). Replacing it with a dummy file should be good enough

> It is complicated to actually remove a folder (possible, not worth the effort). Replacing it with an empty folder should be good enough. Add the folder to the replace list in "config.sh" in the module template, it will replace the folder with an empty one

- In some case, replacing file or folder with empty one is not enough and might cause issue, some modding require files to be disappeared in order to take effect. So Magisk Delta has added removal support for modules: Create the broken symlink which points to `/xxxxx` into the corresponding location of module directory

- Example creating symbolic link as `/data/adb/modules/mymodule_id/system/vendor/etc/thermal-engine-normal.conf`, the target `/vendor/etc/thermal-engine-normal.conf` will be ignored and disappeared

```
ln -s "/xxxxx" /data/adb/modules/mymodule_id/system/vendor/etc/thermal-engine-normal.conf
```

