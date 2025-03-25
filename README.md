# Ansible Role: Camino-Messenger-Bot

![Molecule/Lint](https://img.shields.io/github/actions/workflow/status/lfkdev/ansible-role-camino-messenger-bot/ci.yml)

An Ansible Role that installs and configures Camino Messenger Bot(s) on linux servers.

### Requirements
None.

## Role Variables

### Binary Installation Variables

| Variable                      | Default Value                                                               | Description                                                                   |
| ----------------------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `camino_bot_version`          | `"v10.1.0"`                                                                 | The version of the Camino Messenger Bot to download.                          |
| `camino_bot_download_url`     | `"https://github.com/chain4travel/camino-messenger-bot/releases/download/{}/camino-messenger-bot-linux-amd64-{{ camino_bot_version }}.tar.gz"` | URL to download the bot tarball.                                              |
| `camino_bot_tarball_dest`     | `"/tmp/camino-messenger-bot.tar.gz"`                                        | Temporary destination path for the downloaded tarball.                      |
| `camino_bot_extract_path`     | `"/tmp"`                                                                    | Directory where the tarball will be extracted.                              |
| `camino_bot_binary_src`       | `"camino-messenger-bot"`                                                    | The name of the binary inside the tarball.                                  |
| `camino_bot_binary_dest`      | `"/usr/local/bin/camino-messenger-bot"`                                     | Destination path for the installed binary.                                  |
| `camino_bot_systemd_path`     | `"/etc/systemd/system"`                                                     | Directory to place the systemd service files.                               |
| `camino_config_path`          | `"/etc/camino-messenger-bot"`                                               | Directory where configuration files will be stored.                         |
| `camino_bot_user`             | `"camino"`                                                                  | System user to run the bot under.                                           |

### Bot Configuration Variables

These variables are used to generate the bot configuration file. They support per‑bot overrides, allowing each bot to have its own settings. If not overridden in the `camino_bots` list, the default values are used.

| Variable                                                   | Default Value                                                  | Description                                                                              |
| ---------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `camino_messenger_bot_developer_mode`                      | `true`                                                         | Enable lower level logging for development.                                              |
| `camino_messenger_bot_response_timeout`                    | `10000`                                                        | Timeout (in ms) for responses to messages via Matrix.                                    |
| `camino_messenger_bot_booking_token_address`               | `"0x123"`                   | Address used for minting and buying Booking Tokens.                                      |
| `camino_messenger_bot_chain_rpc_url`                       | `"wss://columbus.camino.network/ext/bc/C/ws"`                   | C-Chain RPC URL.                                                                         |
| `camino_messenger_bot_bot_key`                             | `"YOUR_PRIVATE_KEY_HEX"`                                         | Bot’s private key (hex, without 0x prefix).                                               |
| `camino_messenger_bot_cm_account_address`                  | `"0xYOUR_CM_ACCOUNT_ADDRESS"`                                    | Bot’s CM account address.                                                                |
| `camino_messenger_bot_cheque_expiration_time`              | `18144000`                                                     | Cheque expiration time in seconds.                                                       |
| `camino_messenger_bot_min_cheque_duration_until_expiration`  | `15552000`                                                     | Minimum duration until cheque expiration.                                                |
| `camino_messenger_bot_cash_in_period`                      | `86400`                                                        | Cash-in period (in seconds).                                                             |
| `camino_messenger_bot_network_fee_recipient_bot_address`   | `"0x123"`                   | Matrix application service bot address.                                                  |
| `camino_messenger_bot_network_fee_recipient_cm_account`    | `"0x123"`                   | Matrix application service CM account address.                                           |
| `camino_messenger_bot_db_migrations_path`                  | `"file://./migrations"`                                          | Path to the migrations directory containing SQL scripts.                                 |
| `camino_messenger_bot_db_path`                             | `"distributor-bot-db"`                                           | Path to the database directory.                                                          |
| `camino_messenger_bot_matrix_host`                         | `"messenger.chain4travel.com"`                                   | Matrix server hostname.                                                                    |

#### Feature-Specific Sections

You can enable or disable whole sections by setting the corresponding `enabled` variable to `true` or `false`.

**Partner Plugin:**

| Variable                                               | Default Value       | Description                                                    |
| ------------------------------------------------------ | ------------------- | -------------------------------------------------------------- |
| `camino_messenger_bot_partner_plugin_enabled`          | `false`             | Set to true to enable the partner plugin.                      |
| `camino_messenger_bot_partner_plugin_host`             | `"localhost:50051"` | Host and port for the partner plugin service.                  |
| `camino_messenger_bot_partner_plugin_unencrypted`      | `true`              | Whether the connection is unencrypted.                         |
| `camino_messenger_bot_partner_plugin_ca_file`          | `"ca-cert.pem"`     | CA file if required (used when encryption is enabled).         |

**RPC Server:**

| Variable                                               | Default Value       | Description                                                    |
| ------------------------------------------------------ | ------------------- | -------------------------------------------------------------- |
| `camino_messenger_bot_rpc_server_enabled`              | `true`              | Set to true to enable the RPC server.                          |
| `camino_messenger_bot_rpc_server_port`                 | `9090`              | Port for the RPC server.                                       |
| `camino_messenger_bot_rpc_server_unencrypted`          | `true`              | Whether the RPC server is unencrypted.                         |
| `camino_messenger_bot_rpc_server_cert_file`            | `"server-cert.pem"` | TLS certificate file for the RPC server.                       |
| `camino_messenger_bot_rpc_server_key_file`             | `"server-key.pem"`  | TLS key file for the RPC server.                               |

**Tracing:**

| Variable                                               | Default Value       | Description                                                    |
| ------------------------------------------------------ | ------------------- | -------------------------------------------------------------- |
| `camino_messenger_bot_tracing_enabled`                 | `false`             | Set to true to enable tracing.                                 |
| `camino_messenger_bot_tracing_cert_file`               | `"server-cert.pem"` | TLS certificate file for tracing.                              |
| `camino_messenger_bot_tracing_host`                    | `"localhost:4317"`  | Host for tracing data collection.                              |
| `camino_messenger_bot_tracing_insecure`                | `true`              | Set to true to disable TLS verification.                       |
| `camino_messenger_bot_tracing_key_file`                | `"server-key.pem"`  | TLS key file for tracing.                                      |

### Bot List

Define one or more bots in the `camino_bots` list. Each bot must specify at least:

- `name`: A unique identifier for the bot.
- `config`: The filename for the bot's configuration file.
- Your keys/secrets

You can override any of the configuration variables on a per‑bot basis. For example:

```yaml
camino_bots:
  - name: camino-bot-distributor
    camino_messenger_bot_bot_key: "YOUR_PRIVATE_KEY_HEX"
    camino_messenger_bot_cm_account_address: "0xYOUR_CM_ACCOUNT_ADDRESS"
    camino_messenger_bot_developer_mode: false
    camino_messenger_bot_tracing_enabled: false
    camino_messenger_bot_rpc_server_enabled: true
    camino_messenger_bot_partner_plugin_enabled: false

  - name: camino-bot-supplier
    camino_messenger_bot_bot_key: "YOUR_PRIVATE_KEY_HEX"
    camino_messenger_bot_cm_account_address: "0xYOUR_CM_ACCOUNT_ADDRESS"
    camino_messenger_bot_developer_mode: false
    camino_messenger_bot_tracing_enabled: false
    camino_messenger_bot_rpc_server_enabled: false
    camino_messenger_bot_partner_plugin_enabled: true

### Dependencies
- Currently, only the Debian family is supported (feel free to submit a PR for RedHat).

### Example Playbook
```yaml
- hosts: servers
  roles:
    - { role: lfkdev.camino_messenger_bot }
```

### License
MIT
