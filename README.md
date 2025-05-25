### Beszel Overview

**Beszel** is a lightweight, self-hosted monitoring solution for tracking the status and performance of servers and containers.

### Setup Summary

Beszel was deployed using Ansible with Docker as the runtime. The setup includes two parts:

1. **Beszel Hub**
   Installed inside a Docker container on a dedicated host. Ansible automates Docker installation and then runs the Beszel Hub container.

   * Installs Docker on the host (if not already installed)
   * Runs the Beszel Hub container, which collects system activity from agents

3. **Beszel Agent**
   Deployed on target servers using a separate Ansible playbook. This playbook:

   * Installs Docker on the host (if not already installed)
   * Runs the Beszel Agent container, which reports system activity back to the Hub

Each component is containerized for isolation and simplicity, with Ansible ensuring consistent, automated deployment.

### Playbook Execution Order

Run the following Ansible playbooks in order:

1. `hub_docker_beszel.yml`
   Installs Docker (if not present) and deploys the Beszel Hub in a Docker container on a new VM.

```
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                    NAMES
383751bd634e   henrygd/beszel   "/beszel serve --htt…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->8090/tcp   beszel
```

```
root@debian:~# lsof -i :8080
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
docker-pr 6629 root    7u  IPv4  38013      0t0  TCP *:http-alt (LISTEN)
```

3. `agent_docker_beszel.yml`
   Installs Docker (if not present) and deploys Beszel Agent containers on the VMs to be monitored.


### Steps to Use Beszel for System Monitoring

1. **Access the Beszel Hub UI**
   Open your browser and go to the IP or domain where the Beszel Hub is running (e.g., `http://<hub-ip>:8080`).

   ![Screenshot 2025-05-22 at 10.08.44 PM.png](attachment:0c130b85-6bb1-4b54-b527-e858ae3ca3f9:Screenshot_2025-05-22_at_10.08.44_PM.png)

3. **Create a New Account**
   Sign up using the web interface to access the dashboard.

4. **Copy the Provided SSH Key**
   After logging in, copy the SSH key shown in the agent setup instructions.

   ![Screenshot 2025-05-22 at 10.59.12 PM.png](attachment:82b43c62-c1c0-449a-a13e-2d8ee71c6f0d:Screenshot_2025-05-22_at_10.59.12_PM.png)

6. **Update Agent Variables**
   Paste the SSH key into the `agent_docker_beszel.yml` playbook variables.

7. **Configure Inventory**
   Add the target hosts in `inventory/hosts.ini` under a relevant group.

8. **Run the Agent Playbook**
   Execute the playbook to install the Beszel Agent on each target system:

   ```bash
   ansible-playbook playbooks/agent_docker_beszel.yml
   ```

9. **Add the System in the UI**
   Go back to the Beszel UI and register the monitored system.

   ![Screenshot 2025-05-22 at 11.04.48 PM.png](attachment:712d3a44-84c1-4114-8890-6150eaacd242:Screenshot_2025-05-22_at_11.04.48_PM.png)

11. **Start Monitoring**
   You should now see real-time system activity and metrics in the dashboard.

![Screenshot 2025-05-22 at 11.05.32 PM.png](attachment:a2e6cc06-5bac-4a54-ad01-fb1de637de3b:Screenshot_2025-05-22_at_11.05.32_PM.png)

![Screenshot 2025-05-22 at 11.06.18 PM.png](attachment:56b0443e-4fe2-45de-b2fa-131f2c3b8d9d:Screenshot_2025-05-22_at_11.06.18_PM.png)

