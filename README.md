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

<img width="1368" alt="Screenshot 2025-05-26 at 5 31 16 AM" src="https://github.com/user-attachments/assets/a7af1125-685d-47c8-9c01-e1814ad195a9" />

2. **Create a New Account**
   Sign up using the web interface to access the dashboard.

3. **Copy the Provided SSH Key**
   After logging in, copy the SSH key shown in the agent setup instructions.

<img width="407" alt="Screenshot 2025-05-26 at 5 31 45 AM" src="https://github.com/user-attachments/assets/31190b3a-f602-49ea-ad97-8ee82642fc43" />

4. **Update Agent Variables**
   Paste the SSH key into the `agent_docker_beszel.yml` playbook variables.

5. **Configure Inventory**
   Add the target hosts in `inventory/hosts.ini` under a relevant group.

6. **Run the Agent Playbook**
   Execute the playbook to install the Beszel Agent on each target system:

   ```bash
   ansible-playbook playbooks/agent_docker_beszel.yml
   ```

7. **Add the System in the UI**
   Go back to the Beszel UI and register the monitored system.

<img width="671" alt="Screenshot 2025-05-26 at 5 32 26 AM" src="https://github.com/user-attachments/assets/d09988b6-a662-45cf-b18f-48bb01ea85ac" />


8. **Start Monitoring**
   You should now see real-time system activity and metrics in the dashboard.
<img width="1372" alt="Screenshot 2025-05-26 at 5 32 52 AM" src="https://github.com/user-attachments/assets/15807e44-ab59-4c4e-aaa5-acc03bbd2806" />

<img width="803" alt="Screenshot 2025-05-26 at 5 33 34 AM" src="https://github.com/user-attachments/assets/14a1ea77-2981-420f-afe8-95832766a660" />
