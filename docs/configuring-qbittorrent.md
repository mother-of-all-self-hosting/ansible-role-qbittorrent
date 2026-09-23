<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up qBittorrent

This is an [Ansible](https://www.ansible.com/) role which installs [qBittorrent](https://www.qbittorrent.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

qBittorrent is a BitTorrent client programmed in C++ / Qt that uses libtorrent.

See the project's [documentation](https://github.com/qbittorrent/qBittorrent/wiki/) to learn what qBittorrent does and why it might be useful to you.

## Adjusting the playbook configuration

To enable qBittorrent with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# qbittorrent                                                          #
#                                                                      #
########################################################################

qbittorrent_enabled: true

########################################################################
#                                                                      #
# /qbittorrent                                                         #
#                                                                      #
########################################################################
```

### Set the hostname

To enable qBittorrent you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
qbittorrent_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

>[!NOTE]
> The `qbittorrent_path_prefix` variable can be adjusted to host under a subpath (e.g. `qbittorrent_path_prefix: /qbittorrent`), but this hasn't been tested yet.

### Exposing the torrenting port (optional)

To become an "active node", add the following configuration to your `vars.yml` file, and configure port-forwarding in your router:

```yaml
qbittorrent_container_torrenting_bind_port: "{{ qbittorrent_container_torrenting_port }}"
```

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `qbittorrent_environment_variables_additional_variables` variable

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, qBittorrent becomes available at the specified hostname like `https://example.com`.

To get started, open the URL with a web browser to log in to the instance with a **temporary** randomly generated password. You can output the password by running the playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=print-qbittorrent-password
```

After logging in to the instance as the `admin` user with the password, you need to change it under `Tools -> Options -> WebUI` in the `Authentication` section. The generated password is a temporary  one, and will change on each start-up.

### Integration with Sonarr/Radarr

To add qBittorrent to your [Sonarr](https://sonarr.tv/) or [Radarr](https://radarr.video/) instance navigate to the form at `Settings > Download Clients > Add > qBittorrent`:

Set the `host` field to your qBittorrent URL (without the protocol) and `port` as 443. Make sure to click `Use SSL`. Set the `username` and `password` fields as your qBittorrent credentials.

Fill in the rest of the form with your preferences, and you're done!

![Sonarr Add Download Client](./assets/sonarr-add-download-client.webp)

>[!NOTE]
> If you are looking for an Ansible role for Sonarr and Radarr, you can check out [ansible-role-sonarr](https://github.com/spatterIight/ansible-role-sonarr) and [ansible-role-radarr](https://github.com/spatterIight/ansible-role-radarr).

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu qbittorrent` (or how you/your playbook named the service, e.g. `mash-qbittorrent`).
