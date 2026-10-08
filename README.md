# local_manifests

Local manifests for building PixelOS (Android 17 / cp2a) for the Galaxy S20 family
(x1s, y2s, z3s) on the Exynos9830-Development device trees.

## Usage

```
repo init -u <PixelOS default manifest URL> -b seventeen
git clone https://github.com/Tobim6/local_manifests .repo/local_manifests -b seventeen
repo sync
```

Then, e.g. for z3s:

```
source build/envsetup.sh
lunch custom_z3s cp2a user
mka bacon
```

## Files

- `z3s.xml` — Galaxy S20 Ultra (SM-G988B) device/vendor trees, kernel (forked for ReSukiSU),
  shared `universal9830-common` device tree (forked, carries most of the real fixes: eSIM/eUICC,
  open-source IMS, ion sepolicy, thermal HAL, libsec-ril shim, Wi-Fi overlay, brightness, etc.),
  Samsung hardware repos.
- `y2s.xml` — Galaxy S20+ (SM-G986B) device/vendor trees. Shares the kernel and
  `universal9830-common` tree from `z3s.xml` (sync both files together).
- `x1s.xml` — Galaxy S20 (SM-G981B) device/vendor trees, same sharing as y2s.
- `ims.xml` — swaps AOSP's stock `ImsStack`/`ImsMedia`/`CarrierSettings` for krazey's
  open-source IMS stack (VoLTE/VoWiFi for this platform). The phone-UID fix for ImsStack
  lives on `Tobim6/ImsStack`'s `z3s-local` branch, not on krazey's own revision — change
  the `revision`/`remote` in this file to pull that in instead of krazey's pinned commit.
- `openeuicc-upstream.xml` — OpenEUICC (open-source LPA) from estkme-group, used for eSIM
  support across the whole device family.

## Notes

Device-tree-level product config bugs fixed along the way (apply to the shared tree, so
relevant for any device added later): `soong_config_set` vs `soong_config_set_bool` type
mismatches for shared audio/wifi vars, the z3s-only `sec.android.hardware.nfc@1.2-service`
blob that was wrongly declared in the shared `device-common.mk` (now relocated to z3s's own
`device.mk`), and the unbuildable `SamsungEuicc` proprietary package reference in x1s's
upstream device tree (removed — the shared tree's `OpenEUICC` already covers it).
