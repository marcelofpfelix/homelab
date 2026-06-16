# homelab

Homelab automation using Ansible

See [docs/private-data.md](docs/private-data.md) for how private inventory,
templates, and secrets are handled.

### Configuration management

```console
  ## examples
ansible-playbook server-config.yml"
```

### Deploy Containers

```console
  ## examples
ansible-playbook deploy_container.yml -e "container=container_name target=hostname"
```

### Home



### Infrastructure as code
