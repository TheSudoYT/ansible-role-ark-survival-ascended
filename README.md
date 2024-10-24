# ark-survival-ascended-ansible-role
This Ansible role installs and configures an ARK Survival Ascended Dedicated Server, including optional NFS mounts, SteamCMD installation, game configuration, optional backups, and log rotation on Linux.

## Supported Operating Systems

- Debian ( apt package manager)

## Requirements
- This role is designed to run on Debian-based systems unless future support is added for others.
- Ensure sufficient disk space (at least 20GB) in the ARK installation directory. The role will fail if <20GB is free in `the ark_install_directory_path`

## Variables
The following variables can be set to customize the role:

| Variable                      | Default Value                                  | Description                                                                                   |
|--------------------------------|------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `ark_install_directory_path`   | `/ark-asa`                                     | Path where the ARK server will be installed. Must have >20GB of free space.                                                 |
| `steam_install_directory_path` | `/opt/steam`                                   | Path where SteamCMD and Proton will be installed.                                              |
| `ge_proton_version`            | `9-16`                                         | The version of GE Proton to download and install.                                              |
| `template_game_user_settings`  | `true`                                         | Use Jinja templating to install a `GameUserSettings.ini` or copy from a user-specified path.   |
| `systemd_service_name`         | `ark-island`                               | The name of the systemd service for managing the ARK server.                                   |
| `configure_backups`            | `true`                                         | Whether to configure automatic backups and create the backup script and cron job.              |
| `cron_minute`                  | `"*/30"`                                       | Cron schedule minute field for the backup script.                                              |
| `cron_hour`                    | `"*"`                                          | Cron schedule hour field for the backup script.                                                |
| `cron_day`                     | `"*"`                                          | Cron schedule day field for the backup script.                                                 |
| `cron_month`                   | `"*"`                                          | Cron schedule month field for the backup script.                                               |
| `cron_weekday`                 | `"*"`                                          | Cron schedule weekday field for the backup script.                                             |
| `dir_to_backup`                | `"/ark-asa/ShooterGame/Saved"`                 | Directory that will be backed up.                                                              |
| `backup_dir`                   | `"/opt/ark-cluster/ASACluster/backups/island"` | Directory where the backup files will be stored.                                               |
| `cluster_dir_to_backup`        | `"/opt/ark-cluster/ASACluster/clusters"`       | Directory to back up if `backup_cluster` is set to `true`.                                     |
| `cluster_backup_dir`           | `"/opt/ark-cluster/ASACluster/backups/clusters"`| Directory to store cluster backups if `backup_cluster` is `true`.                              |
| `backup_cluster`               | `false`                                        | Whether to back up the cluster directory. Only set to `true` on one host in the cluster.       |
| `map_name`                     | `Aberration_WP`                                | The name of the map for the ARK server.                                                        |
| `session_name`                 | `middle-earth-aberration`                      | The session name for the ARK server.                                                           |
| `auto_save_period_minutes`     | `10.0`                                         | The auto-save interval for the ARK server, in minutes.                                         |
| `clusterID`                    | `fellowship`                                   | The cluster ID used for cluster settings.                                                      |
| `ClusterDirOverride`           | `/opt/ark-cluster/ASACluster`                  | The directory override for the ARK cluster.                                                    |
| `ServerAdminPassword`          | `"password"`                                  | The admin password for the ARK server.                                                         |
| `port`                         | `7779`                                         | The game server port for the ARK server.                                                       |
| `QueryPort`                    | `27017`                                        | The query port for the ARK server.                                                             |
| `WinLiveMaxPlayers`            | `20`                                           | The maximum number of players allowed on the server.                                           |
| `ServerPassword`               | `"password"`                              | The password required to join the server.                                                      |
| `RCONEnabled`                  | `True`                                         | Whether RCON (remote console) is enabled.                                                      |
| `RCONPort`                     | `27022`                                        | The port used for RCON.                                                                        |
| `mods`                         | []                               | List of mods to be installed.  use_mods must be true                                                               |
| `use_mods`                     | `false`                                         | Whether mods should be enabled on the ARK server.                                              |
| `NoBattleEye`                  | `true`                                         | Whether to disable BattleEye on the server.                                                    |
| `mount_nfs`                    | `true`                                         | Whether to mount NFS shares.                                                                   |
| `nfs_shares`                   | `[{ path: "/opt/ark-cluster", server: "nas.lab.com:/mnt/nas/ark" }]` | List of NFS shares to mount.                                                  |
| `game_user_settings_path` | "" | If template_game_user_settings is false then provide the local path to an existing GameUserSettings.ini to copy. |


## Tags
The role includes the following tags for easier task management:

- nfs: Manages NFS mounts, directories, and persistence.
- update_user_data: Manages systemd service and configuration files (e.g., GameUserSettings.ini).
- backups: Manages the creation of backups, cron jobs, and log rotation.

## Example Playbook

```yaml
---
- name: Install and configure ARK Survival Ascended server
  hosts: ark-island-server
  become: yes
  vars:
    ark_install_directory_path: "/ark-asa"
    steam_install_directory_path: "/opt/steam"
    ge_proton_version: "9-16"
    template_game_user_settings: true
    game_user_settings_path: ""
    systemd_service_name: "ark-aberration"
    configure_backups: true
    cron_minute: "*/30"
    cron_hour: "*"
    cron_day: "*"
    cron_month: "*"
    cron_weekday: "*"
    dir_to_backup: "/ark-asa/ShooterGame/Saved"
    backup_dir: "/opt/ark-cluster/ASACluster/backups/aberration"
    cluster_dir_to_backup: "/opt/ark-cluster/ASACluster/clusters"
    cluster_backup_dir: "/opt/ark-cluster/ASACluster/backups/clusters"
    backup_cluster: false
    map_name: "Aberration_WP"
    session_name: "middle-earth-aberration"
    auto_save_period_minutes: 10.0
    clusterID: "fellowship"
    ClusterDirOverride: "/opt/ark-cluster/ASACluster"
    ServerAdminPassword: "password"
    port: 7779
    QueryPort: 27017
    WinLiveMaxPlayers: 20
    ServerPassword: "password"
    RCONEnabled: "True"
    RCONPort: 27022
    mods:
      - 927131
      - 914844
      - 916922
    use_mods: true
    NoBattleEye: true
    mount_nfs: true
    nfs_shares:
      - { path: "/opt/ark-cluster", server: "nas.lab.com:/mnt/nas/ark" }

  roles:
    - ark_server
```