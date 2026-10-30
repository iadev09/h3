# Publishing the fork

Publish from `forked/0.0.8` to crates.io. Receive upstream changes from
[hyperium/h3](https://github.com/hyperium/h3); send fork branches and release
tags only to the fork repository selected by the maintainer.

## Prepare

1. Fetch upstream and merge the selected upstream commit into the fork branch.
   Validated contributions awaiting review may also be merged from their
   contribution branches. Retain those commits in the fork's ancestry so the
   same changes can be reconciled when upstream accepts or revises them.
   Preserve the fork package names, aliases, metadata, and README files when
   resolving conflicts; review upstream changes to dependency versions and
   features rather than discarding them. Keep application-specific integration
   in consuming projects. Preserve released history and tags.
2. Record the upstream commit, any unmerged contribution commits and PR links,
   and fork-specific changes in the release notes. Distinguish accepted upstream
   changes from contributions still under review. Keep the initial provenance
   in the README as a historical reference, and label unreleased APIs separately
   from the latest published version.
3. All four packages share one version, starting at `0.0.1`. For each subsequent
   family release, increase `[workspace.package].version` in the root
   `Cargo.toml`, then update all intra-family version requirements and README
   examples to that version. Publish all four packages together, including
   those whose implementation did not change. A merge without publication does
   not require a version increase. Versions belong to this fork independently
   of upstream: the upstream `0.0.8` baseline uses fork versions `0.0.1`,
   `0.0.2`, and so on. If upstream `0.0.9` is adopted as the next baseline,
   start the whole fork family at `0.1.0` and continue in the `0.1.x` line.
4. Check the four packages, examples, and documentation tests with the intended
   feature combinations. Verify that the dependency graph uses the forked H3
   family and official Quinn, without Git dependencies or private registries.
5. Review package contents and run `cargo publish --dry-run -p <package>`.
   Dependent packages cannot resolve unpublished fork dependencies from
   crates.io; dry-run each dependent package after its prerequisites are
   published and available in the index. Local workspace checks do not replace
   these registry checks.

## Publish

Publish in dependency order:

```sh
cargo publish -p h3-forked
cargo publish -p h3-datagram-forked
cargo publish -p h3-quinn-forked
cargo publish -p h3-webtransport-forked
```

Before publishing each dependent package, confirm that its prerequisite
versions are available from crates.io. The path-only `h3-datagram-forked` dev
dependency in `h3-forked` is for workspace tests and is omitted from its published
manifest; it does not create a publication cycle.

Tag each published package as `<package>-v<version>`, for example
`h3-forked-v0.0.1`, and push the release branch and those tags to the fork.
Create release notes identifying the upstream commit and the package versions.
Never publish fork releases or tags to the upstream repository.
