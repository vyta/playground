# Burst Header WASM filter

This is based off the work in this repo: https://github.com/retaildevcrews/istio

## Compile Rust to WASM

```sh
rustup update
rustup target add wasm32-unknown-unknown
cargo build --target wasm32-unknown-unknown --release
```

## Set-up cluster

```sh
# kind create cluster
kind create cluster --config kind.yaml

# install istio
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system
helm install istiod istio/istiod -n istio-system --wait

# deploy echo app
kubectl label namespace default istio-injection=enabled --overwrite
kubectl apply -f echo.yaml

# deploy metric server used by bursting service
kubectl apply -f metrics.yaml 

# Create HPA for echo
kubectl autoscale deployment echo --cpu-percent=40 --min=1 --max=2

# deploy bursting-metrics svc
kubectl apply -f ngsa.yaml
```

## EnvoyFilter + ConfigMap

```sh
# Create configmap
kubectl create cm burst-wasm-filter --from-file=./target/wasm32-unknown-unknown/release/burst_header.wasm 

# Mount configmap into proxy via patch
kubectl patch deployment echo --patch-file=echo-patch.yaml

# Apply filter
kubectl apply -f envoy-filter.yaml
```

## WasmPlugin Resource

This bascially does many of the steps above automatically

- WASM needs to be published to a registry to be cached correctly
- cache pods are DaemonSets on each node (this is not ideal and is being improved). Ideally modules could be streames to the proxy directly over HTTP (landing soon)
```sh
# Push wasm binary to local registry
# configure kind w/ local registry
# deploy WasmPlugin
```
## Verify

```sh
# Test with httpie
http http://localhost:30000
```

You should see the following header:
`x-load-feedback: service=default/echo, current-load=1, target-load=1, max-load=2`