# SUNDAY component updates (Windows x64)

The public feed is `OrkaLxrd/sunday-updates` GitHub Releases. The client verifies
the exact manifest bytes with a pinned RSA-3072/SHA-256 public key before using
URLs or paths. Each downloaded component and extracted file is hash-checked.

Components are application binaries, Python, generator, tools, resources and
individual printer vendors. Only components containing changed files download;
unchanged files inside a downloaded component are not replaced. This is component
patching, not binary-delta patching. There is no automatic deletion of obsolete files.

The UI displays current/new versions, changelog and compressed download size.
Skip applies to a release sequence, not all future updates. Manual checks ignore
the skip preference. Updates require the user to save and close SUNDAY; the updater
does not terminate the application or discard projects.

The updater runs from a temporary copy to allow replacing its installed binary.
Before replacement it backs up affected files and writes a recovery journal.
Failures roll back; an interrupted journal is recovered on the next updater run.
User settings, project files, plugin state and accounts are outside the allowlist.
Backup directories are retained under `.sunday-update` in the installation.

## Build and release

1. `powershell -ExecutionPolicy Bypass -File build.ps1` builds the Windows updater.
2. Build a fresh, clean installer stage including `SUNDAY-Updater.exe`.
3. Run `publish.py --stage CLEAN_STAGE --version X.Y.Z --sequence INCREASING_INT
   --notes CHANGELOG.txt --out NEW_OUTPUT_DIRECTORY`.
4. Create a **draft** GitHub release tagged `vX.Y.Z`, upload the generated ZIPs,
   `windows-x64.json` and `windows-x64.json.sig`. Test before publishing as latest.
5. Full installers must also include the matching signed manifest/signature as
   `.sunday-update/installed.json` and `.sunday-update/installed.sig` to report the
   correct installed version. An original 0.8.0 installation has no receipt.

Private key: `%USERPROFILE%/.sunday-signing/update-private.pem`, protected by a
user-only ACL. Never upload it, package it or regenerate it for an existing feed.
Back it up securely. `UpdatePublicKey.cs` contains only the public verification key.
Updates are not Apple/notarization or Windows Authenticode signatures.

macOS updates are not implemented by this Windows updater. No live releases have
been published as part of initial implementation.
