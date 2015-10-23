tls-client-hello
================

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

```rust
use tls_client_hello::ClientHelloBuilder;

fn get_server_name() -> String {
    // get bytes from new connection
    let bytes: Vec<u8> = read(client);

    // Get the client_hello frame
    let client_hello = ClientHelloBuilder::parse_bytes(&bytes[..]).unwrap();

    // Find SNI extension
    for ext in client_hello.extensions {
        // zero indicates the SNI extension
        if ext.extension_type == 0 {
            return str::from_utf8(&ext.extension_data[..]).unwrap().to_owned();
        }
    }
}
```

