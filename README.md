
# Ansible Role: Docker

An Ansible role that installs [Docker](https://www.docker.com) on Linux, with optional Docker Compose and Docker plugin support.

__Please only install this role when CI is PASSING!__

![CI Status](https://github.com/bsmeding/ansible_role_docker/actions/workflows/ci.yml/badge.svg)  
Supported platforms: Ubuntu, Debian, Rocky Linux (RedHat-based), Pop!_OS, and Linux Mint.

Downloads: ![Ansible Role](https://img.shields.io/ansible/role/d/bsmeding/docker)

This role is based on [geerlingguy.docker](https://github.com/geerlingguy/ansible-role-docker/) and includes the following enhancements:

- Removes Podman on RedHat-based systems.
- Adds user and group `docker:docker`.
- Adds the current Ansible become user to the Docker group.
- Sets `docker_uid` and `docker_gid` to Docker user and group IDs, enabling seamless integration across roles using Docker.

## Requirements
None.

## Role Variables

### Docker Packages and Edition

```yaml
# Docker edition ('ce' for Community Edition, 'ee' for Enterprise Edition)
docker_edition: 'ce'
docker_packages:
  - "docker-{{ docker_edition }}"
  - "docker-{{ docker_edition }}-cli"
  - "docker-{{ docker_edition }}-rootless-extras"
  - "containerd.io"
docker_packages_state: present
```

- **`docker_edition`**: Choose between `ce` (Community Edition) or `ee` (Enterprise Edition).
- **`docker_packages_state`**: Set to `present`, `latest`, or `absent` to control Docker package state.

### Proxy Settings

```yaml
http_proxy: ''
https_proxy: ''
no_proxy: ''
```

Define proxy settings if required.

### Service Management

```yaml
docker_service_manage: true
docker_service_state: started
docker_service_enabled: true
docker_restart_handler_state: restarted
```

Control the Docker service state, enabling or disabling it at boot.

### Docker Compose Plugin

```yaml
docker_install_compose_plugin: true
docker_compose_package: docker-compose-plugin
docker_compose_package_state: present
```

Settings for the Docker Compose Plugin, which allows `docker compose` commands.

### Docker Compose Standalone

```yaml
docker_install_compose: false
docker_compose_version: "v2.20.3"
docker_compose_arch: "{{ ansible_architecture }}"
docker_compose_url: "https://github.com/docker/compose/releases/download/{{ docker_compose_version }}/docker-compose-linux-{{ docker_compose_arch }}"
docker_compose_path: /usr/local/bin/docker-compose
```

Install Docker Compose as a standalone binary.

### Repository Management

```yaml
docker_add_repo: true
docker_repo_url: https://download.docker.com/linux
```

Control repository setup. Set `docker_add_repo: false` to skip adding the Docker repository.

### Debian/Ubuntu Settings

```yaml
docker_apt_release_channel: stable
docker_apt_ansible_distribution: "{{ 'ubuntu' if ansible_distribution in ['Pop!_OS', 'Linux Mint'] else ansible_distribution }}"
docker_apt_arch: "{{ 'arm64' if ansible_architecture == 'aarch64' else 'amd64' }}"
docker_apt_repository: "deb [arch={{ docker_apt_arch }} signed-by=/etc/apt/trusted.gpg.d/docker.asc] {{ docker_repo_url }}/{{ docker_apt_ansible_distribution | lower }} {{ ansible_distribution_release }} {{ docker_apt_release_channel }}"
docker_apt_ignore_key_error: true
docker_apt_gpg_key: "{{ docker_repo_url }}/{{ docker_apt_ansible_distribution | lower }}/gpg"
docker_apt_gpg_key_checksum: "sha256:1500c1f56fa9e26b9b8f42452a553675796ade0807cdce11975eb98170b3a570"
docker_apt_filename: "docker"
```

Settings specific to Debian/Ubuntu distributions.

### RedHat/CentOS Settings

```yaml
docker_yum_repo_url: "{{ docker_repo_url }}/{{ (ansible_distribution == 'Fedora') | ternary('fedora','centos') }}/docker-{{ docker_edition }}.repo"
docker_yum_repo_enable_nightly: '0'
docker_yum_repo_enable_test: '0'
docker_yum_gpg_key: "{{ docker_repo_url }}/centos/gpg"
```

Settings specific to RedHat-based distributions.

### User and Group Management

```yaml
docker_users: []
```

A list of system users to add to the `docker` group.

### Docker Daemon Options

```yaml
docker_daemon_options: {}
```

Configure Docker daemon options, such as enabling remote access by adding the following:

```yaml
docker_daemon_options:
  hosts:
    - "unix:///var/run/docker.sock"
    - "tcp://127.0.0.1:2375"
```

> ⚠️ **Warning**: Enabling remote access can expose the host to unauthorized access. Use TLS certificates to secure the connection.

## Error Handling

If you encounter the error `"Error connecting: Error while fetching server API version: Not supported URL scheme http+docker"`, try upgrading the following Ansible collections or downgrading the `requests` library:

```yaml
  - name: community.general
  - name: community.docker
```

## Author Information

Originally created by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/), and adapted with additional features by Bart Smeding.

