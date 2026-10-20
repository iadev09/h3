# h3-webtransport-forked

The WebTransport extension in the
[h3-forked family](https://github.com/iadev09/h3/tree/forked/0.0.8). It depends on
`h3-forked` and `h3-datagram-forked`.

Add the crates.io dependency:

```toml
[dependencies]
h3-webtransport = { package = "h3-webtransport-forked", version = "0.0.2" }
```

The Rust crate name remains `h3_webtransport`. See the
[upstream documentation](https://github.com/hyperium/h3/tree/master/h3-webtransport)
for protocol details and the
[local example](https://github.com/iadev09/h3/blob/forked/0.0.8/examples/webtransport_server.rs)
for this fork's API. The
[fork README](https://github.com/iadev09/h3/blob/forked/0.0.8/README.md) describes
provenance and maintenance policy.

## Connection and session ownership

The connection driver and WebTransport sessions have separate lifetimes:

- `WebTransportConnection::new(h3_connection)` retains the HTTP/3 connection.
  Keep calling `accept()` to process control traffic and receive both ordinary
  HTTP and WebTransport streams. Resolve each bidirectional stream independently
  so incomplete HEADERS do not block other requests.
- `accept_session(request, stream)` returns an owned acceptance future. Sending
  the CONNECT response does not borrow the driver. An accepted
  `WebTransportSession` holds its CONNECT stream and outgoing capabilities;
  dropping it does not close the HTTP/3 connection.
- Dispatch incoming WT bidi/uni streams by session ID. Obtain one datagram
  reader from the driver and dispatch datagrams by their associated stream ID;
  independent per-session readers would compete for the same input.
- `session.open_bi()`, `session.open_uni()` and `session.send_datagram()` are
  bound to that session. `driver.shutdown(max_requests)` initiates HTTP/3
  GOAWAY; the caller still owns session tasks and their completion.

The driver does not spawn tasks or buffer streams for applications. The caller
owns concurrent resolvers, session admission, routing (including streams that
arrive before acceptance), session closure, and drain policy. Keep the driver
alive and polled while session work is active.

This source API replaces the connection-owning `WebTransportSession::accept`
API from fork version `0.0.1`. Session `accept_bi`/`accept_uni` calls move to
the driver's `accept`; its result distinguishes bidi resolvers from uni streams.
The old per-session datagram reader moves to the driver, and outgoing stream
methods no longer accept another session's ID. This change ships in fork `0.0.2`.

This is an independent, experimental fork, not an official Hyperium release.
Licensed under [MIT](https://github.com/iadev09/h3/blob/forked/0.0.8/LICENSE), with
original copyright notices preserved.
