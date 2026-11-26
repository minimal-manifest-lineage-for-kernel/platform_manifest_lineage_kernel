# Minimal LineageOS kernel manifest

## Sync

```bash
mkdir lineage-kernel-build && cd lineage-kernel-build
repo init -u https://github.com/minimal-manifest-lineage-for-kernel/platform_manifest_lineage_kernel -b lineage-22.2
repo sync -c -j$(nproc) --no-tags --optimized-fetch
```

Do not use `repo init --depth=1` for the manifest repository; large projects
already use `clone-depth="1"`.

## Build

```bash
source build/envsetup.sh
lunch lineage_<device>-bp1a-userdebug
export ALLOW_MISSING_DEPENDENCIES=true

m kernel
m dtbimage
m dtboimage
m vendor_dlkmimage     # when BOARD_USES_VENDOR_DLKMIMAGE := true
m system_dlkmimage     # when BOARD_USES_SYSTEM_DLKMIMAGE := true
m odm_dlkmimage        # when BOARD_USES_ODM_DLKMIMAGE := true
```

`bp1a` is the Android 15 release configuration used by LineageOS 22.2;
`trunk_staging` is an AOSP development configuration and is not the real
release target.

`ALLOW_MISSING_DEPENDENCIES=true` only skips intentionally omitted Android
modules. It does not replace device repositories or kernel sources.

## Native targets

| Target | Output |
| --- | --- |
| `m kernel` | `$OUT/kernel`, build objects and modules |
| `m dtbimage` | `$OUT/dtb.img` |
| `m dtboimage` | `$OUT/dtbo.img` |
| `m vendor_dlkmimage` | `$OUT/vendor_dlkm.img` |
| `m system_dlkmimage` | `$OUT/system_dlkm.img` |
| `m odm_dlkmimage` | `$OUT/odm_dlkm.img` |

`m bootimage`, `m vendorbootimage`, `m bootimage_debug` and
`m vendorbootimage_debug` are not supported; this tree has no ramdisk or
Android userland for them.

## Troubleshooting

- `repo sync` reports `invalid upstream ...^1`: run
  `git -C .repo/manifests fetch --unshallow` and sync again.
- `m` reports missing modules: the flag only ignores omitted Android modules.
- `m kernel` reports a missing config fragment: the kernel branch does not
  contain a `.config` entry from `TARGET_KERNEL_CONFIG`.
- `bootimage` / `vendorbootimage` fail by design; use the kernel targets above.

## License

Apache-2.0. Synced projects retain their own licenses.
