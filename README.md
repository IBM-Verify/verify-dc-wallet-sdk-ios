# IBM Verify DC Wallet SDK for iOS

IBM's distribution of the [EUDI Wallet Kit library for iOS](https://github.com/eu-digital-identity-wallet/eudi-lib-ios-wallet-kit).

This fork exists to give IBM control over packaging (pinned transitive dependency versions),
documentation, and distribution stability via semver tags. It carries the minimal possible
code delta over upstream — patches are upstreamed as soon as practical and dropped from this
fork once merged.

---

## Requirements

- iOS 16.0+
- Swift 6.0+
- Xcode 16.0+

---

## SPM Integration

Add the package to your `Package.swift` or via **Xcode → Add Package Dependencies**:

```swift
.package(url: "https://github.com/ibm-verify/verify-dc-wallet-sdk-ios.git", from: "1.0.0")
```

Then add `EudiWalletKit` as a target dependency:

```swift
.target(
    name: "MyTarget",
    dependencies: [
        .product(name: "EudiWalletKit", package: "verify-dc-wallet-sdk-ios")
    ]
)
```

---

## Upstream Version Mapping

| IBM Fork Version | Upstream `eudi-lib-ios-wallet-kit` | Upstream SHA | IBM Patches | Date |
|---|---|---|---|---|
| `v1.0.0` | `v0.35.2` | `181a53cbefaf` | [Fix log entries overwriting each other](#ibm-patches) | 2026-08-19 |

### IBM Patches

#### `v1.0.0` — Fix log entries overwriting each other

**File:** `Sources/EudiWalletKit/Services/FileLogging.swift`

Upstream `v0.35.2` uses `FileHandle(forWritingTo:)` followed by `seekToEndOfFile()` to initialise
the file log handler. On some platforms this overwrites the existing file content instead of
appending to it. The IBM patch replaces this with a POSIX `open(path, O_WRONLY | O_CREAT | O_APPEND)`
call so that log entries are always appended correctly.

An upstream PR will be raised to incorporate this fix. Once merged upstream this patch will be
dropped from the next IBM fork release.

---

## IBM Patch Policy

- **Upstream first.** Any fix or feature that is generally useful is raised as an upstream PR
  at the same time it is carried here.
- **Patches are temporary.** A patch is dropped from this fork as soon as it is merged upstream
  and included in a new upstream release that this fork syncs to.
- **Breaking upstream changes.** If a new upstream release contains breaking API changes that
  require app changes, the IBM fork version is bumped to the next `MAJOR`. The previous IBM
  version tag remains available for pinning until the app migration is complete.

---

## How to Sync Upstream

A full step-by-step process is documented in
[`verify-dc-fork-roadmap.md`](https://github.ibm.com/IBM-Verify/verify-app-v3-ios/blob/main/verify-dc-fork-roadmap.md)
in the consuming app repository.

Brief steps:

1. Assess the upstream release notes — identify breaking changes and decide MAJOR/MINOR/PATCH bump
2. Create a `sync/vX.Y.Z` branch from `main`
3. `git merge upstream/vX.Y.Z` — resolve conflicts only in `Package.swift` and `README.md`
4. Re-apply IBM patches that have not yet been merged upstream; drop those that have
5. Update `Package.swift` dependency versions to match upstream's resolved versions
6. Update the **Upstream Version Mapping** table above
7. Open a PR into `main`, merge, tag, and create a GitHub Release
8. Update the consuming app's `Package.swift` to the new version

---

## Versioning Scheme

This fork uses independent semver (`vMAJOR.MINOR.PATCH`) starting at `v1.0.0`.

| Segment | When to increment |
|---|---|
| `MAJOR` | Upstream breaking API changes that require changes in the consuming app |
| `MINOR` | Sync to a new upstream release with no breaking changes to IBM consumers |
| `PATCH` | IBM-only hotfix between upstream syncs |

---

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for the full text.
