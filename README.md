# open-iscsi

This role installs and configures open-iscsi, an iSCSI initiator.

## Requirements

Debian

## Role Variables

| Variable                               | Default / Mandatory | Description                                                                                                                                                                                                                            |
|:---------------------------------------|:--------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `open_iscsi_initiator_name`            | :heavy_check_mark:  | Name of this initiator name, see [iSCSI naming conventions](https://pubs.vmware.com/vsphere-4-esx-vcenter/index.jsp?topic=/com.vmware.vsphere.config_iscsi.doc_41/esx_san_config/storage_area_network/c_iscsi_naming_conventions.html) |
| `open_iscsi_automatic_startup`         | `False`             | Automatically startup a session on service initialization                                                                                                                                                                              |
| `open_iscsi_leading_login`             | `False`             | For automatic startup, try login on each available ifaces until one succeeds instead of trying to login on all ifaces simultaneously                                                                                                   |
| `open_iscsi_authentication`            | `False`             | Use CHAP authentication to login into nodes, see [Login authentication](#login-authentication)                                                                                                                                         |
| `open_iscsi_discovery_authentication`  | `False`             | Use CHAP authentication for discovery, see [Discovery authentication](#discovery-authentication)                                                                                                                                       |
| `open_iscsi_replacement_timeout`       | `120`               | Timeout in seconds for session reestablishment before failing SCSI commands. Set to `0` to fail immediately and negative to leave the commands queued until the session is logged back in or manually logged out.                      |
| `open_iscsi_login_timeout`             | `15`                | Timeout in seconds to wait for login to complete                                                                                                                                                                                       |
| `open_iscsi_logout_timeout`            | `15`                | Timeout in seconds to wait for logout to complete                                                                                                                                                                                      |
| `open_iscsi_noop_out_interval`         | `5`                 | Interval in seconds to wait for on connection before sending a ping                                                                                                                                                                    |
| `open_iscsi_noop_out_timeout`          | `5`                 | Timeout in seconds to wait for a Nop-out response before failing the connection                                                                                                                                                        |
| `open_iscsi_abort_timeout`             | `15`                | Timeout in seconds to wait for an abort response before trying a LUN reset                                                                                                                                                             |
| `open_iscsi_lu_reset_timeout`          | `30`                | Timeout in seconds to wait for a LUN response before failing the operation and trying session re-establishment                                                                                                                         |
| `open_iscsi_tgt_reset_timeout`         | `30`                | Timeout in seconds to wait for a target response before failing the operation and trying session re-establishment                                                                                                                      |
| `open_iscsi_initial_login_retry_max`   | `8`                 | Number of retries after `open_iscsi_login_timeout` is reached                                                                                                                                                                          |
| `open_iscsi_cmds_max`                  | `128`               | Number of commands the session will queue, must be a power of two between `2` and `2048`.                                                                                                                                              |
| `open_iscsi_queue_depth`               | `32`                | Device's queue depth, a power of two between `2` and `1024`                                                                                                                                                                            |
| `open_iscsi_thread_priority`           | `-20`               | Thread priority                                                                                                                                                                                                                        |
| `open_iscsi_initialR2T`                | `False`             | Wait for a R2T command before sending data                                                                                                                                                                                             |
| `open_iscsi_immediate_data`            | `True`              | Send data to target unsolicitedly                                                                                                                                                                                                      |
| `open_iscsi_first_burst_length`        | `262144`            | Maximum number of unsolicited bytes sent in an iSCSI PDU (see `open_iscsi_immediate_data`), must be between 512 and 2^24-1.                                                                                                            |
| `open_iscsi_max_burst_length`          | `16776192`          | Maximum payload size in bytes to negotiate, must be between 512 and 2^24-1.                                                                                                                                                            |
| `open_iscsi_max_recv_length`           | `262144`            | Maximum payload size the initiator can receive, must be between 512 and 2^24-1.                                                                                                                                                        |
| `open_iscsi_max_xmit_length`           | `0`                 | Maximum data bytes to send, must be between 512 and 2^24-1. If set to `0`, the initiator will use the target's `max_recv_length`.                                                                                                      |
| `open_iscsi_discovery_max_recv_length` | `32768`             | Data bytes the initiator can receive during a discovery session, must be between 512 and 2^24-1.                                                                                                                                       |
| `open_iscsi_header_digest`             | `None`              | Digest methods to use for the header, see [Digests](#digests)                                                                                                                                                                          |
| `open_iscsi_data_digest`               | `None`              | Digest methods to use for data, see [Digests](#digests)                                                                                                                                                                                |
| `open_iscsi_nr_sessions`               | `1`                 | Number of sessions per iface record. It may be useful to use more than one session for multipath setups.                                                                                                                               |
| `open_iscsi_fast_abort`                | `True`              | Do not respond to PDUs after a management function like an `ABORT`.                                                                                                                                                                    |

### Login authentication
These login credentials for logging into targets are only used when `open_iscsi_authentication` is `True` and have to be defined in username/password pairs.
You can choose to define one or both of the pairs.
| Variable                      | Description                                                |
|:------------------------------|:-----------------------------------------------------------|
| `open_iscsi_auth_username`    | Username to authenticate at the target                     |
| `open_iscsi_auth_password`    | Password to authenticate at the target                     |
| `open_iscsi_auth_username_in` | Username to authenticate the target against this initiator |
| `open_iscsi_auth_password_in` | Password to authenticate the target against this initiator |

### Discovery authentication
These login credentials for the discovery phase are only used when `open_iscsi_discovery_authentication` is `True` and have to be defined in username/password pairs.
You can choose to define one or both of the pairs.
| Variable                                | Description                                                |
|:----------------------------------------|:-----------------------------------------------------------|
| `open_iscsi_discovery_auth_username`    | Username to authenticate at the target                     |
| `open_iscsi_discovery_auth_password`    | Password to authenticate at the target                     |
| `open_iscsi_discovery_auth_username_in` | Username to authenticate the target against this initiator |
| `open_iscsi_discovery_auth_password_in` | Password to authenticate the target against this initiator |

### Digests
Digests can be disabled with `None` or checked with `CRC32C`.
It's also possible to allow both methods seperated by a comma, depending on the target's ability.
If two methods are specified, the first one is the preferred one.
E.g. `CRC32C,None` asks the target to check the digest, but does not enforce it.

## Example Playbook

```yml
- hosts: hypervisor
  roles:
  - open_iscsi_authentication: True
    open_iscsi_initiator_name: 'iqn.2013-07.de.uni-stuttgart.stuvus.iscsi:hypervisor01'
	open_iscsi_auth_username: hypervisor01
	open_iscsi_auth_password: weef4IeYe0ahPahbe9oj1jia6leiv0jaigaanie1pes4biuB2u
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).


## Author Information

- [Michel Weitbrecht (SlothOfAnarchy)](https://github.com/SlothOfAnarchy) _michel.weitbrecht@stuvus.uni-stuttgart.de_
