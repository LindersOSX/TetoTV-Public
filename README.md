# Public launch and switching channels

The app already pins its Public feed to
`LindersOSX/TetoTV-Public` and its Beta feed to
`LindersOSX/TetoTV-Beta`. Nothing needs to be added to the Public repository's
Code page for discovery. Updates are GitHub **Releases**, with downloadable
assets attached. As checked on November 13, 2026, Public's latest-release API
returns 404 because no release is published.

**GitHub preparation still required:** the Public repository's current
[release-verification workflow](https://github.com/LindersOSX/TetoTV-Public/blob/main/.github/workflows/verify-release-assets.yml)
was read-only checked on November 13, 2026. It accepts only old Beta releases
and hard-codes a Beta release title. Before tagging the first Public build,
update that repository with the matching reviewed source and a Public-capable
verification workflow/tooling. Do not copy a Beta-only publisher unchanged or
remove the existing source/signing/native-review checks to make it pass.
The old workflow runs after publication with read-only permissions, so a
failed check does not itself hide or delete the release from the app's feed.
No remote files, tags, releases or repository settings were modified here.

Public starts at **1.0**, followed by names such as **1.2, 1.3, 1.4**; Beta
keeps **1.x.x** (and historical 2.x.x). Skipping a display number is fine.
Settings → System → App updates → Update channel works without Developer Mode.
Selecting a channel now checks its feed immediately without automatically
downloading or installing anything. Automatic checks subsequently follow that
selected feed. The installed build, not the selected feed, controls Beta-only
runtime features.

## Before building

Choose the actual Public name and a production build number. Any `1.0` /
`410089` values below are examples, not a release already built or approved.
Keep the existing application/package ID and production signing key. This is
one app with two feeds, not a second app with a different data directory.

Android cannot install a lower build code over a newer one. Keep build numbers
increasing across both repositories. Matching Public/Beta counterparts may use
the same build code; an explicitly selected counterpart can then install in
either direction with the same signer. A newer Beta cannot switch in-place to
an older-code Public APK until a compatible Public build is available. Changing
the feed never uninstalls the app or bypasses OS restrictions.

## Publish Public Android

1. Build the signed universal APK with the real two-part Public build name and
   production build number. Merely renaming a Beta APK does not make it Public.
2. Create a GitHub release in **TetoTV-Public** with tag `v1.<Public number>`
   (first release: `v1.0`) and attach its matching universal APK. Uploading a file
   to the repository's Code tab does not publish an app update.
3. Keep it a normal completed release, not Draft or Pre-release, and set it as
   Latest in that repository.
4. Keep the Android build-code marker in the release notes. For an APK actually
   built as `410089`, the marker would be:

   ```html
   <!-- tetotv-android-version-code: 410089 -->
   ```

   Replace that number with the APK's actual build code. The display version
   alone cannot reliably decide Public/Beta update precedence. No separate
   Android update JSON is required.
5. Upload matching source, native-source and verification assets required by
   the existing release-review policy. Review/approval gates remain unchanged;
   this work does not publish or approve a Public release.

## Public Windows

Windows now accepts Public `v1.x` tags and verifies the manifest and installer
against the **Public** repository, never the Beta repository. Attach both the
installer and the generated `TetoTV-v1.x-windows-x64-update.json` to the same
Public release. Do not rename either or edit size/SHA-256 fields manually.

Use `tool/release/build_public_windows.ps1` with the chosen `-Version`,
`-BuildNumber` and new `-OutputDirectory`; `-FlutterPath` can identify Flutter
when it is not on PATH. Flutter needs a three-component numeric version
internally, so the wrapper builds `1.x.0` but the validated native override
preserves **1.x** as the displayed, reported and updater version. The wrapper
restores its process-local environment afterward. Normal Beta builds are
unchanged. Do not directly pass an unnormalized `1.x` to Flutter's Windows
builder; Flutter would fall back to an incorrect native version.

The packager refuses an EXE whose actual version/build differs from the Public
version/build requested. Installation identity, data location and account
storage stay the same. Existing Windows signature/security approval prompts
still apply.

## Before announcing

- Check the Public release is visible while signed out and its APK downloads.
- Check for updates from an older compatible Android and Windows installation.
- Check both Public → Beta and Beta → Public, using compatible build codes.
- Confirm settings/accounts survive installation; keep a backup for testing.
- Current 1.0.88 release artifacts have not been rebuilt by this change. Do not
  upload the old Beta binaries under a Public filename.

Both repositories retain independent Latest releases. The old retained-2.0.80
bridge marker is not needed for a new ordinary release.
