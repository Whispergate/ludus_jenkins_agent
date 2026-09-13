# ludus_jenkins_agent

Ansible role that installs a Jenkins agent on Debian/Ubuntu Linux hosts for the MAAS build pipeline. Connects to the Jenkins controller via WebSocket using the JNLP agent protocol.

## What it does

1. Installs OpenJDK 21 JRE
2. Optionally installs Docker Engine (for running containerised builds)
3. Creates a dedicated `jenkins` system user (added to the `docker` group)
4. Downloads `agent.jar` from the Jenkins controller
5. Creates a systemd service that auto-starts and reconnects on failure
6. Creates a shared `/payloads` directory for artifact collection

## Requirements

- Debian 12 or Ubuntu 22.04/24.04
- Network access to the Jenkins controller
- The agent must be pre-configured in Jenkins (matching the `ludus_jenkins_agent_name`)

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_jenkins_url` | `http://jenkins:8080` | Jenkins controller URL |
| `ludus_jenkins_agent_name` | `{{ inventory_hostname }}` | Agent name (must match Jenkins node config) |
| `ludus_jenkins_agent_labels` | `linux-generic` | Space-separated labels for job assignment |
| `ludus_jenkins_agent_workdir` | `/opt/jenkins-agent` | Agent working directory |
| `ludus_jenkins_agent_user` | `jenkins` | System user to run the agent |
| `ludus_jenkins_agent_install_docker` | `true` | Install Docker Engine |
| `ludus_jenkins_agent_jar_version` | `latest` | Agent JAR version (downloaded from controller) |
| `ludus_jenkins_agent_executors` | `2` | Number of executors |

## Example (Ludus range config)

```yaml
- vm_name: '{{ range_id }}-runner-lin01'
  hostname: '{{ range_id }}-runner-lin01'
  template: debian-12-x64-server-template
  vlan: 99
  ip_last_octet: 4
  ram_gb: 8
  cpus: 4
  linux: true
  roles:
    - whispergate.ludus_jenkins_agent
    - whispergate.ludus_maas_builders
  role_vars:
    ludus_jenkins_url: 'http://10.{{ range_second_octet }}.99.3:8080'
    ludus_jenkins_agent_name: runner-lin01
    ludus_jenkins_agent_labels: 'linux-c linux-go linux-nim linux-generic'
```

## License

BSD-2-Clause
