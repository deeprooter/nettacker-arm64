# nettacker-arm64
An optimized, production-ready **Docker Compose deployment** configuration for running the **OWASP Nettacker** automated penetration testing framework natively on **Raspberry Pi (ARM64)** hardware.

## Project Overview
**OWASP Nettacker** is a powerful automated framework for penetration testing, vulnerability scanning, and network reconnaissance. However, because it is traditionally built and documented for standard **x86/64 architectures**, deploying it on low-power ARM devices like the Raspberry Pi usually comes with a headache. Users often run into frustrating file-path inconsistencies, permission blocks, missing data persistence, and system port overlaps.

This repository solves those issues with a refined, single-command **Docker Compose stack** explicitly compiled and verified for **ARM64** single-board computers. Instead of forcing you to run manual command-line tools inside an isolated container, this setup transitions Nettacker into a resilient, background-running web application. By configuring automated Web API mode, opening a dedicated external port to avoid network conflicts, and establishing clean volume mappings, this configuration ensures scan logs are stored in you home directory of host computer and scan results/ reports are which are stored in backend **SQLite database** are provided inside the UI as visual HTML reports.


## Key Features & Optimizations

- **Native ARM64 Architecture Alignment:** Engineered for locally compiled Docker images on modern Raspberry Pi OS environments.
- **Persistent Data Volume Mapping:** Syncs your operational files directly to `/home/<usr>/Nettacker_Reports`. Your databases (`nettacker.db`) and text logs won't evaporate when the container stops or rebuilds.
- **Port Collision Prevention:** Re-routes the default internal application port to host port **`5001`**, keeping standard port `5000` clear for other web servers or development pipelines running on your SBC-host.
- **Automated Startup Integration:** Feeds argument flags directly through the container orchestration layer, dropping you instantly into the Web UI engine without requiring manual terminal interactive commands.
- **Resilient Infrastructure:** Implements production directives like `restart: unless-stopped` alongside interactive terminal buffers (`tty: true`, `stdin_open: true`) to ensure the server automatically recovers from hardware reboots or random errors.




---

## Installation (See package/DEPLOY.md for package deployment)

### 1. Prerequisites
Ensure you have Docker and Docker Compose installed on your Raspberry Pi:
```bash
sudo apt update
sudo apt install docker.io docker-compose-v2 -y
```

### 2. Create the Local Reports Directory
Set up the absolute file path on your local host to catch incoming logs and scan reports:
```bash
mkdir -p /home/<usr_name>/Nettacker_Reports
sudo chmod -R 755 /home/<usr_name>/Nettacker_Reports
```

### 3. Deploy the Stack
Create a file named `docker-compose.yml` and paste the configuration block below:

```yaml
services:
  nettacker:
    image: owasp/nettacker:local
    container_name: nettacker-api
    restart: unless-stopped
    tty: true
    stdin_open: true
    ports:
      - "5001:5000"
    volumes:
      - /home/<usr>/Nettacker_Reports:/usr/src/owasp-nettacker/.data  # replace <usr> with your userid with here
    command: [
      "--start-api", 
      "--api-host", "0.0.0.0", 
      "--api-access-log", "/usr/src/owasp-nettacker/.data/nettacker.log"
    ]

```

Launch the stack using your terminal (or paste the code block directly into a **Dockhand** Stack Deployment panel):
```bash
sudo docker compose up -d
```

---

## Accessing the Interface

### Step 1: Retrieve Your Security Token
Because the API server generates a unique, automated access key on its initial boot, fetch the key directly from the active runtime logs:
```bash
sudo docker logs nettacker-api
```
Look through the boot tracking text lines to find your `API Access Key`.

<img width="2096" height="1619" alt="nettracker-API" src="https://github.com/user-attachments/assets/a6982492-a46d-4b36-be24-d81bb9e17d7a" />


### Step 2: Open the Web UI Dashboard

<img width="3634" height="1823" alt="homepage-nettracker-UI" src="https://github.com/user-attachments/assets/88285d4a-4c58-4287-8e6d-f2f11258ac47" />

**Open your web browser of choice on your local area network and navigate to:**

```text
https://<YOUR_RASPBERPY_PI_IP>:5001
```

*Note: If navigating directly on the Pi screen itself, use `https://localhost:5001`. Paste the retrieved access token into the login box to initialize the engine.*

---

## Accessing Your Scan Reports
- **Real-Time Operational Log:** Access via your Pi at `/home/<usr>/Nettacker_Reports/nettacker.log`.
- **Relational Databases:** The scan records are stored locally inside the SQLite format engine at `/home/<usr>/Nettacker_Reports/nettacker.db`.
- **HTML Scan Files:** While running in Web API mode, files are generated dynamically upon demand. Use the **Results/Search** panel inside the browser GUI to view graphs or trigger a localized browser download link.
 
<img width="3609" height="1936" alt="nettracker-scan-results" src="https://github.com/user-attachments/assets/2692afbb-f053-40ac-aeb3-e036f9106833" />

---

## License
Distributed under the **Apache License 2.0**. See the `LICENSE` file for more details. Compatible with the primary upstream project standards of the OWASP foundation.
