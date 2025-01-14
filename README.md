[crates-badge]: https://img.shields.io/crates/v/webscan.svg
[crates-url]: https://crates.io/crates/webscan
[license-badge]: https://img.shields.io/crates/l/webscan.svg
[examples-url]: https://github.com/shellrow/webscan/tree/main/examples

# webscan [![Crates.io][crates-badge]][crates-url] ![License][license-badge]
Cross-platform web scan library  
with the aim of being lightweight and fast. 

## Features
- URI SCAN
- DOMAIN SCAN

## Usage
Add `webscan` to your dependencies  
```toml:Cargo.toml
[dependencies]
webscan = "0.2.0"
```

## Example
URI Scan Example
```rust
extern crate webscan;
use webscan::{UriScanner, ScanStatus, RequestMethod};
use tokio;
use std::fs::read_to_string;
use std::time::Duration;

#[tokio::main]
async fn main(){
    let mut uri_scanner = match UriScanner::new(){
        Ok(scanner) => (scanner),
        Err(e) => panic!("Error creating scanner: {}", e),
    };
    let base_uri = String::from("http://192.168.1.8/xvwa/");
    uri_scanner.set_base_uri(base_uri);
    let data = read_to_string("common.txt");
    let text = match data {
        Ok(content) => content,
        Err(e) => {panic!("Could not open or find file: {}", e);}
    };
    let word_list: Vec<&str> = text.trim().split("\n").collect();
    for word in word_list {
        uri_scanner.add_word(word.to_string());
    }
    uri_scanner.set_request_method(RequestMethod::Head);
    uri_scanner.set_timeout(Duration::from_millis(20000));
    uri_scanner.set_accept_invalid_certs(false);
    uri_scanner.run_scan().await;
    let result = uri_scanner.get_result();
    print!("Status: ");
    match result.scan_status {
        ScanStatus::Done => {println!("Normal end")},
        ScanStatus::Timeout => {println!("Timed out")},
        _ => {println!("Error")},
    }
    println!("URI Scan Result:");
    for (uri, status) in result.responses {
        println!("{} {}", uri, status);
    }
    println!("Scan Time: {:?}", result.scan_time);
}
```

For more details see [Examples][examples-url]

## Supported platform
- Linux
- macOS(OS X)
- Windows
