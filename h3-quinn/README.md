# h3-quinn-forked

The Quinn adapter in the [h3-forked family](https://github.com/iadev09/h3/tree/forked/0.0.8).
This independent, experimental fork connects `h3-forked` to the official Quinn
`0.11` series and accepts `quinn::Connection` directly.

Add the crates.io dependency:

```toml
[dependencies]
h3 = { package = "h3-forked", version = "0.0.2" }
h3-quinn = { package = "h3-quinn-forked", version = "0.0.2", features = ["datagram"] }
```

The Rust crate name remains `h3_quinn`. The optional `datagram` feature uses
`h3-datagram-forked`; `tracing` retains its upstream behavior. Configure Quinn's
runtime and TLS features in your application.

For virtual hosting on a shared QUIC endpoint, use
[`quinn-deferred-config`](https://github.com/iadev09/quinn-deferred-config)
with the official Quinn stack. It lets the application select each virtual
host's `rustls::ServerConfig` from the ClientHello's SNI during the handshake.
The resulting `quinn::Connection` can be passed directly to this adapter.
We maintain both projects and will coordinate updates to keep their
supported Quinn and Rustls versions compatible. Deferred configuration is
optional and is not a dependency of this adapter.

See the [upstream adapter](https://github.com/hyperium/h3/tree/master/h3-quinn)
and [Quinn documentation](https://docs.rs/quinn) for usage, and the
[fork README](https://github.com/iadev09/h3/blob/forked/0.0.8/README.md) for provenance
and maintenance policy. This is not an official Hyperium release.

Licensed under [MIT](https://github.com/iadev09/h3/blob/forked/0.0.8/LICENSE), with
original copyright notices preserved.
