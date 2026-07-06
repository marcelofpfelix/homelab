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

### Deploy Local Services

```console
ansible-playbook deploy-service.yml -e deploy_service_name=lagostim-personal-hermes
ansible-playbook deploy-service.yml -e deploy_service_name=lagostim-work-hermes
```

The Lagostim service definitions manage macOS LaunchAgents that run the local
Docker Compose profiles from `~/gwt/marcelofpfelix/lagostim/main` and use
runtime env files under `~/docker/lagostim/<profile>/runtime/compose.env`.

### Home



### Infrastructure as code
