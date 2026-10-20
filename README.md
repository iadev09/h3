# h3-forked

An independently published fork of [hyperium/h3](https://github.com/hyperium/h3).
It makes merged upstream HTTP/3 and WebTransport changes available without
waiting for an upstream release or requiring consumers to use Git dependencies,
Cargo patches, or a private registry.

This is not an official Hyperium release. The implementation remains
experimental; publishing this fork does not imply API stability or production
readiness.

## Packages

All four packages live in this repository and depend on the forked family:

| Package | Version | Rust crate name |
| --- | --- | --- |
| `h3-forked` | `0.0.2` | `h3` |
| `h3-quinn-forked` | `0.0.2` | `h3_quinn` |
| `h3-datagram-forked` | `0.0.2` | `h3_datagram` |
| `h3-webtransport-forked` | `0.0.2` | `h3_webtransport` |

The four packages share one release version, starting at `0.0.1`. Every family
release advances all four packages together, independently of upstream version
numbers. The `0.0.x` fork line starts at `0.0.1` on the upstream `0.0.8`
baseline. If upstream `0.0.9` is adopted as the next baseline, the fork family
starts a `0.1.x` line at `0.1.0`. Merged changes within the current baseline
advance the current fork line when published.

The shared version is defined in the root `Cargo.toml` under
`[workspace.package]`; intra-family version requirements are updated with it.
The initial fork is based on
upstream commit [`43745ad`](https://github.com/hyperium/h3/commit/43745ad87e95b0c035ac9c547aad467303213425),
including all changes merged through that commit.

## Release 0.0.2

The upstream baseline remains commit
[`43745ad`](https://github.com/hyperium/h3/commit/43745ad87e95b0c035ac9c547aad467303213425):
H3 `0.0.8`, Quinn adapter `0.0.10`, Datagram `0.0.2`, and WebTransport `0.1.2`.
The official Quinn `0.11` dependency is unchanged.

This release separates the WebTransport connection driver from individual
sessions. It is a breaking API change from fork `0.0.1`: consumers must keep
driving incoming HTTP/WT streams and route them by session ID rather than
handing the connection to a session. See the
[WebTransport migration notes](h3-webtransport/readme.md#connection-and-session-ownership).
All four fork packages advance together to `0.0.2`; upstream package versions
have not changed.

## Dependencies

Use the crates.io packages with aliases to retain the upstream Rust import
names:

```toml
[dependencies]
h3 = { package = "h3-forked", version = "0.0.2" }
h3-quinn = { package = "h3-quinn-forked", version = "0.0.2", features = ["datagram"] }
h3-webtransport = { package = "h3-webtransport-forked", version = "0.0.2" }
```

For direct HTTP Datagram access, add:

```toml
h3-datagram = { package = "h3-datagram-forked", version = "0.0.2" }
```

`h3-quinn-forked` uses the official Quinn `0.11` series and accepts its
`quinn::Connection` directly. Add `quinn` with the runtime and TLS features your
application needs. A separate Quinn fork or Hyper integration is not required.
Use this fork family consistently wherever H3 types cross crate boundaries;
aliasing `h3-forked` does not change another dependency's upstream `h3` types.

### Using quinn-deferred-config

For virtual hosting on a shared QUIC endpoint, this family can be used with
[`quinn-deferred-config`](https://github.com/iadev09/quinn-deferred-config).
During the QUIC handshake, the application uses the ClientHello's SNI to select
the virtual host's `rustls::ServerConfig`, including its certificate and TLS
policy, within the official Quinn stack. The resulting `quinn::Connection` is
used directly by `h3-quinn-forked` for HTTP/3 and WebTransport.

We maintain both projects. Updates to this fork and `quinn-deferred-config`
will be coordinated to keep their supported Quinn and Rustls versions
compatible, so applications can continue to use them together. Applications
choose whether to use deferred configuration; it is not a dependency of the
H3 fork family.

## Upstream and maintenance

See the [upstream README](https://github.com/hyperium/h3#readme) for the project
overview, design, and protocol documentation. The [local examples](https://github.com/iadev09/h3/tree/forked/0.0.8/examples)
use this fork's packages. Upstream adapters that depend on the original `h3`
package need to be adapted before they can be used with this fork.

`master` tracks upstream. `forked/0.0.8` holds the package names, dependency
wiring, and fork documentation, and receives upstream changes by merge. The
branch name records the original upstream version; fork releases use their own
shared version. The branch includes merged changes beyond that upstream release.
When upstream edits overlap, preserve this fork's identity while reviewing the
incoming changes. Fork README files describe this distribution and link to
upstream documentation rather than maintaining copies of it.

Report fork packaging or integration issues at
[iadev09/h3](https://github.com/iadev09/h3/issues). Protocol fixes should be
contributed upstream when applicable. See [the release workflow](https://github.com/iadev09/h3/blob/forked/0.0.8/docs/PUBLISH.md)
for publication order and provenance.

## License

[MIT](https://github.com/iadev09/h3/blob/forked/0.0.8/LICENSE). Original copyright notices and contributor attribution are
preserved.
