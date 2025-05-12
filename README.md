<!--
SPDX-FileCopyrightText: 2025 spatterlight

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# qBittorrent Ansible role

This is an [Ansible](https://www.ansible.com/) role which installs [qBittorrent](https://www.qbittorrent.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

This role *implicitly* depends on:

- [`com.devture.ansible.role.playbook_help`](https://github.com/devture/com.devture.ansible.role.playbook_help)
- [`com.devture.ansible.role.systemd_docker_base`](https://github.com/devture/com.devture.ansible.role.systemd_docker_base)

Check [defaults/main.yml](defaults/main.yml) for the full list of supported options.

For an Ansible playbook which integrates this role and makes it easier to use, see the [mash-playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

# Limitations

This role configures qBittorrent with security in mind by doing the following:

1. Running the container as a non-root user
2. Making the filesystem read-only
3. Dropping most capabilities

Unfortunately, due to upstream requirements, some admissions had to be made:

1. Several capabilities related to permissions are added to the container
   - SETUID
   - SETGID
   - CHOWN
   - FOWNER
   - DAC_OVERRIDE
2. A `tmpfs` volume is mounted with `exec` permissions

You can read more about these upstream requirements in the documentation:

1. https://docs.linuxserver.io/misc/non-root/
2. https://docs.linuxserver.io/misc/read-only/
