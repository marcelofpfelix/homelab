# Development Notes

These notes preserve durable ideas that were previously only in ignored scratch
files. Keep private host data, tokens, vault passwords, and real work inventory
in the private overlay.

## Private Overlay

- Keep `vars/work/`, `group_vars/`, `host_vars/`, private templates, private
  scripts, and private tasks outside the public repository.
- Evaluate whether `work: true` should select a private container vars path such
  as `work/vars/containers`.
- Revisit whether the private overlay should stay linked by `make private-link`
  or move to a dedicated private submodule. Prefer the current link approach
  unless the workflow needs independent version pinning.

## Container Host Notes

Candidate Docker daemon settings to model when the role owns Docker host setup:

```json
{
  "data-root": "/data/docker",
  "storage-driver": "overlay2",
  "insecure-registries": ["10.0.0.0/8"],
  "dns": [],
  "live-restore": true,
  "log-driver": "journald",
  "log-level": "warn",
  "log-opts": {
    "tag": "{{.ImageID}} {{.ImageName}} "
  }
}
```

Open questions:

- Review whether CoreDNS should be replaced or supplemented by Unbound.
- Keep Pi-hole, dnsmasq, and CoreDNS examples sanitized before promoting them
  from scratch files.
- Use the private routing role as a reference for FRR host-routing behavior:
  `team-telnyx/infra-roles-host-routing`.

## Naming

Candidate lab host naming pattern:

```text
blab01-desktop01
blab01-nuc01 b01 hydrogen h alfa
blab02-pvm01 b02 helium he bravo
blab03-pvm02 b03 lithium li charlie
blab04-pvm03 b04 beryllium be delta
```

Possible naming pools:

- Greek gods, if the lab stays small.
- NATO alphabet, up to 26 hosts.
- Periodic table, up to 118 hosts.

## Vault

Old scratch vault commands were intentionally not copied here. The supported
secret workflow is documented in `docs/private-data.md`.
