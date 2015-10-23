hello-tls
=========

Parse TLS ClientHello messages

## About

This parser was written to support building a TLS capable proxy based on
subdomains (akin to apache virtual hosts). TLS has an extension named Server
Name Identification (SNI) to support such applications.

The client_hello frame contains all the data that is part of the TLS
ClientHello frame and can be retrieved using the publicly visible members.

Supporting the TLS protocol is an explicit non goal of the project at this
time. However, some of the data types that support it may be implemented as
part of improving the project's API.

Future work will, for now, be focused on improving the API and benchmarking for
the intended use case.

## Example

The following example is from `examples/sni.rs`. Try it out with
`cargo run --example sni`. Normally, the provided bytes would come from a TCP
socket. For purposes of demonstation, they are provided inline.

```rust
extern crate tls_client_hello;

use tls_client_hello::ClientHelloBuilder;
use std::str;

fn main() {
    // Pretend like these are from a socket.
    let bytes: Vec<u8> = vec![0x16, ..];

    let parser = ClientHelloBuilder::new();
    let client_hello = parser.parse_bytes(&bytes[..]).unwrap();

    // Find SNI extension
    for ext in client_hello.extensions {
        // zero indicates the SNI extension
        if ext.extension_type.0 == 0 {
            let name = str::from_utf8(&ext.extension_data[..]).unwrap();
            println!("name: {}", name);
        }
    }
}
```

