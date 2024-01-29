# Ansible Role: Docker Nginx Proxy

This ansible roles clones the GitHub Repo [sebastian13/docker-compose-nginx-proxy](https://github.com/sebastian13/docker-compose-nginx-proxy), installs all requirements and starts a nginx proxy on docker.

## Dependencies

It's required to have docker previously installed. I recommend to use this together with the role [nickjj.docker](https://github.com/nickjj/ansible-docker).

## Example Playbook

```yaml
---
- hosts: docker_servers
  become: true

  roles:
    - role: nickjj.docker
    - role: sebastian13.docker-nginx-proxy

  vars:
    docker__users: ["___"]
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
```

## License

Apache License 2.0
