# FluxCD

## Install Flux CLI

- <https://fluxcd.io/flux/installation/>

## Create fine grained token with

Administration -> Access: Read and write
Contents -> Access: Read and write
Metadata -> Access: Read-only

## Bootstrap FluxCD

```bash
flux bootstrap github \
  --owner=alexbechmann \
  --repository=homelab \
  --branch=main \
  --private=true \
  --path=clusters/homelab \
  --personal
```
