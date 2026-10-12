# Ansible Role: Docker Nginx Proxy

This role clones the GitHub repository [sebastian13/docker-compose-nginx-proxy](https://github.com/sebastian13/docker-compose-nginx-proxy), ensures the shared `/docker` layout is usable for the `docker` group, installs the required packages, creates self-signed test certificates, configures a certbot renewal cron job, and starts the stack with Docker Compose v2.

## Requirements

- Docker must already be installed and the compose plugin available.
- The target host should have a `docker` group.
- Users that need to work inside `/docker` should be members of the `docker` group.

## Default behavior

The role defaults to the following values:

```yaml
# Directory path and permissions
docker_nginx_proxy_dir: "/docker/00-nginx-proxy"
docker_nginx_proxy_dir_owner: "root"
docker_nginx_proxy_dir_group: "docker"
docker_nginx_proxy_dir_mode: "2775"

# Git repository values
docker_nginx_proxy_repo: "https://github.com/sebastian13/docker-compose-nginx-proxy.git"
docker_nginx_proxy_version: "main"
docker_nginx_proxy_force: false
docker_nginx_proxy_remote: "origin"
docker_nginx_proxy_accept_newhostkey: true

# Docker Compose behavior
docker_nginx_proxy_create_network: true
docker_nginx_proxy_start_compose: true
```

This ensures the project directory and the parent `/docker` path are owned by `root:docker` and use the setgid bit so new files and directories inherit the `docker` group.

## Example Playbook

```yaml
---
- hosts: docker_servers
  become: true

  roles:
    - role: sebastian13.docker
    - role: sebastian13.docker_nginx_proxy

  vars:
    docker__users: ["your-user"]
    docker__version: "24.0"
    docker__state: "latest"
    docker__compose_v2_version: ""
    docker__pip_docker_compose_state: "absent"
    docker__default_daemon_json: |
      "log-driver": "local",
      "default-address-pools":
      [
        {"base":"172.17.0.0/16","size":24}
      ]

    # Optional override for a custom fork or branch.
    docker_nginx_proxy_repo: "https://github.com/your-user/docker-compose-nginx-proxy.git"
    docker_nginx_proxy_version: "main"
    docker_nginx_proxy_dir: "/docker/00-nginx-proxy"
    docker_nginx_proxy_dir_group: "docker"
    docker_nginx_proxy_dir_mode: "2775"

    # Optional: disable automatic compose startup
    docker_nginx_proxy_start_compose: true
```

## Docker group setup

A typical host setup looks like this:

```bash
mkdir -p /docker
chown root:docker /docker
chmod 2775 /docker
usermod -aG docker your-user
```

This makes all members of the `docker` group able to work in the shared directory layout without needing root for routine configuration tasks.

## Notes

- The role is intentionally tolerant of an existing checkout with local changes: it keeps the deployment from failing immediately while still surfacing the git warning in the task output.
- The certbot cron job is randomized across a night-only time window by default.
- The compose startup is handled through `community.docker.docker_compose_v2`.

## License

Apache License 2.0
