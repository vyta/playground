# Hello-world example of envoy WASM filter

## Compile Rust to WASM

```sh
rustup update
rustup target add wasm32-unknown-unknown
cargo build --target wasm32-unknown-unknown --release
```

## Run WebService w/ Proxy Filter

```sh
docker-compose build
docker-compose up -d
```

## Validate "Hello world" header was added

```sh
curl -s http://localhost:8000 | grep "Hello world"
```
