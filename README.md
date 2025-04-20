# Automated On-Premise HRMS and Attendance System with Monitoring and CI/CD Using Docker and DevOps Tools

## 📍 PROJECT OVERVIEW

In this project, we will deploy an **on-premise HRMS (Human Resource Management System)** and **Attendance Tracker** for the **FCT College of Nursing Sciences, Gwagwalada**, replacing manual methods with a modern, scalable, and automated infrastructure.

This solution uses `TimeTrex`, P`rometheus`, `Grafana`, and `Jenkins`, all orchestrated with `Docker Compose` on `Ubuntu`(via `VirtualBox`). CI/CD pipelines will be managed locally.

## 🎯 OBJECTIVES

1. Deploy an **HRMS/Attendance** platform (TimeTrex CE) using Docker
2. Set up `Prometheus` and `Grafana` for **metrics monitoring** and v**isualization**.
3. Integrate `Jenkins` for `CI/CD` pipelines to **automate builds** and deployment.
4. Build a self-contained on-premise infrastructure.
5. Ensure extensibility and later cloud migration readiness.

## 🧰 TECHNOLOGIES USED

- **Docker:**	Containerization
- **Docker Compose:**	Service orchestration
- **Ubuntu:**	Host operating system (VirtualBox-based)
- **TimeTrex CE:**	HRMS/Attendance system
- **Prometheus:**	Monitoring & metrics collection
- **Grafana:**	Data visualization
- **Jenkins:**	CI/CD automation

## ✅ PREREQUISITES

Ensure the following are ready on your Ubuntu Virtual Machine:

- ✅ Ubuntu 20.04+ (on VirtualBox)

- ✅ At least 4GB RAM

- ✅ Internet access for pulling images

- ✅ Docker installed

- ✅ Docker Compose installed

## 🛠️ STEP 1: Install `Docker` & `Docker Compose`

### 🐳 Docker Environment Setup Script
### 🔧 Purpose:

To set up the container environment needed to deploy the services.



```bash
#!/bin/bash

# Step 1: Update and upgrade the system
echo "🔄 Updating system packages..."
sudo apt update && sudo apt upgrade -y

# Step 2: Install required dependencies
echo "📦 Installing required packages..."
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

# Step 3: Add Docker's official GPG key
echo "🔐 Adding Docker GPG key..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Step 4: Add Docker repository to APT sources
echo "➕ Adding Docker repository..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Step 5: Update the package index again
echo "🔄 Refreshing package index..."
sudo apt update

# Step 6: Install Docker Engine and CLI
echo "🐳 Installing Docker Engine..."
sudo apt install docker-ce docker-ce-cli containerd.io -y

# Step 7: Enable and start Docker service
echo "🚀 Enabling and starting Docker service..."
sudo systemctl enable docker
sudo systemctl start docker

# Confirmation
echo "✅ Docker installation completed successfully!"
docker --version
```
### ✅ How to Use

1. Save the script as `install_docker.sh`
   
   ![](./docs/img/1.docker-install-sh.png)

2. Make it executable:

    ```bash
    chmod +x install_docker.sh
    ```

3. Run the script:

    ```bash
    ./install_docker.sh
    ```

![](./docs/img/3.run-script.png)

![](./docs/img/2.docker-installed-successfully.png)

### ✅ Check Docker

```bash
docker --version
```

![](./docs/img/4.docker-version.png)

## 📦 Install Docker Compose:

### 🔧 Purpose:

To **install Docker Compose** – a tool for defining and managing multi-container Docker applications.

```bash
# !/bin/bash

# Step 1: Update and upgrade the system
echo "🔄 Updating system packages..."
sudo apt update && sudo apt upgrade -y

# Step 2: Install required dependencies
echo "📦 Installing required packages..."
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

# Step 3: Add Docker's official GPG key
echo "🔐 Adding Docker GPG key..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Step 4: Add Docker repository to APT sources
echo "➕ Adding Docker repository..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Step 5: Update the package index again
echo "🔄 Refreshing package index..."
sudo apt update

# Step 6: Install Docker Engine and CLI
echo "🐳 Installing Docker Engine..."
sudo apt install docker-ce docker-ce-cli containerd.io -y

# Step 7: Enable and start Docker service
echo "🚀 Enabling and starting Docker service..."
sudo systemctl enable docker
sudo systemctl start docker

# Confirmation
echo "✅ Docker installation completed successfully!"
docker --version
```

### ✅ How to Use This Script:

1. Save it as `install_docker_compose.sh`

![](./docs/img/5.docker-compose-script.png)

2. Make it executable:

```bash
chmod +x install_docker_compose.sh
```

3. Run it:

```bash
./install_docker_compose.sh
```

## ⚠️ Error Encountered During Docker Compose Installation

### 📝 Error Summary:

While attempting to install Docker Compose using a shell script that relied on either `curl` or `wget`, the download failed with the following error messages:

**Using `curl`:**

```bash
/usr/local/bin/docker-compose: line 1: syntax error near unexpected token `newline'
/usr/local/bin/docker-compose: line 1: `<!DOCTYPE html>'
```

**Using `wget`:**

```bash
Unable to establish SSL connection.
```

### 🔍 Root Cause:

The error occurred because the system was connected to a **restricted or filtered network** that **blocked or interfered with HTTPS connections to GitHub’s file storage** (particularly `objects.githubusercontent.com`). This resulted in either:

- Receiving an HTML error page instead of the actual Docker Compose binary.
- Failing to establish a secure SSL connection altogether.

These are common in networks with:

- Strict firewalls or content filtering.
- Proxy restrictions.
- DNS-level blocks on file hosting services.

## ✅ Solution Implemented

To resolve the issue:

### ✅ Action Taken: Switched to a Different Network Provider

Changing to a **more open and unrestricted network** (such as a mobile hotspot or an alternative ISP) allowed `wget` and `curl` to access GitHub's file servers without interference.

**After the network switch:**

- The download completed successfully.

- Docker Compose was made executable and verified without error.

![](./docs/img/6.curl-wget-successful.png)

### 💡 Recommendation

- When automating installations that rely on external binaries:

- Always check for network restrictions or proxy settings if downloads fail unexpectedly.

- Consider bundling binaries or offering offline installation alternatives when working in restricted environments.

- Verify the integrity of downloaded files to avoid executing HTML/error content by mistake.

## 📁 STEP 2: Restructure Project Directory and Organize Scripts

### 🔧 Purpose:

To improve readability, reusability, and scalability of the project, I am introducing a structured directory layout. This helps isolate concerns, making the project easier to manage and navigate.

### 📌 NOTE TO READER:

    Previously, scripts and configurations were all stored in a single flat directory. To enhance organization and make future updates easier, I’ve adopted a structured layout following DevOps best practices.

### 🗂️ New Project Structure Overview:

We will organize our project into a modular structure for clarity and maintainability.

```
fctnursing-hrms-devops-attendance-stack/
│
├── scripts/                    # All automation scripts (installations, configs, etc.)
│   ├── install_docker.sh
│   ├── install_docker_compose.sh
│   ├── setup_timetrex.sh
│   ├── setup_prometheus.sh
│   └── setup_grafana.sh
│
├── jenkins/                   # Jenkinsfiles and job scripts
│   └── Jenkinsfile
│
├── docker-compose/           # Docker Compose YAML files for services
│   ├── docker-compose.timetrex.yml
│   ├── docker-compose.monitoring.yml
│   └── docker-compose.jenkins.yml
│
├── config/                   # Configuration files (Grafana dashboards, Prometheus configs)
│   ├── prometheus.yml
│   └── dashboards/
│       └── attendance-dashboard.json
│
├── docs/                     # Documentation files (markdown, reports, screenshots)
│   ├── README.md
│   └── img/
│       └── *.png
│
└── .env                      # Environment variables (if any)
```

**🔎 Note:** Each service has its own folder and docker-compose.yml file to ensure modular deployment. A root-level docker-compose.yml can also be used to bring all services up together (optional for CI/CD workflows).

## 🛠️ STEP 3: Create the Directory and Move Files

### 🧰 Instructions:

1. Navigate to your project `root`:
   
   ```bash
   cd ~/Documents/darey-learning-ubuntu/projects/fctnursing-hrms-devops-attendance-stack
   ```
2. Create subdirectories:
   
   ```bash
   mkdir -p scripts jenkins docker-compose config/dashboards docs/img
   ```

3. Move your existing scripts into the scripts/ folder:
   
   ```bash
   mv install_docker.sh scripts/
   mv install_docker_compose.sh scripts/
   # Move other setup scripts you already have as well
   ```
    ![](./docs/img/7.move-scripts.png)

4. **(Optional)** Move any configuration files you’ve created so far:
   
   ```bash
   mv prometheus.yml config/
   mv attendance-dashboard.json config/dashboards/
   ```

5. Move your documentation files into `docs`/:
   
   ```bash
   mv README.md docs/
   mv img docs/
   ```
   ![](./docs/img/8.mv-readme-img-to-docs.png)


## ✅ STEP 4: Create `.env` file

**📄 Purpose:** The `.env` file holds environment variables used in the `docker-compose.yml` file. This allows dynamic configuration, easier management of `secrets`, `ports`, and `versioning` without **hardcoding** them.

### ✅ Create the .env file in your project root:

**Path: `fctnursing-hrms-devops-attendance-stack/.env`**

![](./docs/img/9.envi-file.png)



## STEP 5: Create `docker-compose.yml` with Custom Docker Network for Isolation

### ✨ Purpose:

Define and orchestrate all the core containers (TimeTrex, Prometheus, Grafana, Jenkins) while isolating them within a custom Docker network.

```bash
# version: "3.8"

# Define a custom Docker bridge network for isolation and communication between services
networks:
  hrms_stack_net:   # ✅ Use plain text instead of ${DOCKER_NETWORK}
    driver: bridge

services:

  # =======================
  # 🕒 TimeTrex Service
  # =======================
  timetrex:
    image: ${TIMETREX_IMAGE}             # Pull TimeTrex image from GitHub Container Registry
    container_name: timetrex             # Name of the running container
    ports:
      - "${TIMETREX_PORT}:80"            # Map host port (from .env) to container port 80
    volumes:
      - timetrex_data:/var/www/html      # Persist TimeTrex data across restarts
    networks:
      - ${DOCKER_NETWORK}                # Attach to custom Docker network
    restart: unless-stopped              # Automatically restart container unless it was manually stopped

  # =======================
  # 📈 Prometheus Service
  # =======================
  prometheus:
    image: ${PROMETHEUS_IMAGE}           # Use official Prometheus image
    container_name: prometheus           # Name of the container
    ports:
      - "${PROMETHEUS_PORT}:9090"        # Expose Prometheus on defined host port
    volumes:
      - ${PROMETHEUS_CONFIG}:/etc/prometheus/prometheus.yml   # Mount custom config
    networks:
      - ${DOCKER_NETWORK}                # Connect to the custom network
    restart: unless-stopped              # Restart unless manually stopped

  # =======================
  # 📊 Grafana Service
  # =======================
  grafana:
    image: ${GRAFANA_IMAGE}              # Use official Grafana image
    container_name: grafana              # Name of the container
    ports:
      - "${GRAFANA_PORT}:3000"           # Host port to access Grafana dashboard
    volumes:
      - grafana_data:/var/lib/grafana    # Persist dashboards and config
      - ./config/dashboards:/etc/grafana/provisioning/dashboards  # Mount dashboard config
      - ./config/datasources:/etc/grafana/provisioning/datasources  # Mount data sources (to be created next)
    networks:
      - ${DOCKER_NETWORK}                # Attach to custom Docker network
    restart: unless-stopped              # Restart automatically if crashed

  # =======================
  # 🛠️ Jenkins CI/CD Service
  # =======================
  jenkins:
    image: ${JENKINS_IMAGE}              # Use Jenkins LTS image
    container_name: jenkins              # Container name
    ports:
      - "${JENKINS_HTTP_PORT}:8080"      # Jenkins Web UI port
      - "${JENKINS_AGENT_PORT}:50000"    # Jenkins agent communication port
    volumes:
      - jenkins_data:/var/jenkins_home   # Persist Jenkins jobs and config
    networks:
      - ${DOCKER_NETWORK}                # Attach to custom Docker network
    restart: unless-stopped              # Restart unless manually stopped

# =======================
# 📦 Volume Definitions
# =======================
volumes:
  timetrex_data:                         # Volume to persist TimeTrex data
  grafana_data:                          # Volume to persist Grafana dashboards
  jenkins_data:                          # Volume to persist Jenkins job history and config
```

**🔧 Prometheus Configuration Summary**

**Global Settings:**

- scrape_interval and evaluation_interval are both set to 15 seconds, meaning Prometheus scrapes targets and evaluates rules every 15s.

**Scrape Configurations:**

- TimeTrex: Monitors metrics from the TimeTrex container on internal port 80.
  - Jenkins: Collects metrics from Jenkins running on port 8080.
  - Prometheus: Prometheus scrapes its own metrics on port 9090 for self-monitoring.
  - Grafana: (If metrics plugin is enabled) Prometheus scrapes Grafana metrics from port 3000.


## 📦 STEP 7: Import Attendance Dashboard into Grafana

### 🎯 Purpose:

We want to **visualize monitoring data** (e.g., from `TimeTrex`, `Jenkins`) using **Grafana dashboards**.
This step involves adding a **prebuilt dashboard** to `Grafana` using a `JSON configuration` file.

### ⚙️ Configure Grafana to Automatically Load Dashboard

1. Update your `docker-compose.yml` (or your main compose file):

```bash
  grafana:
    image: ${GRAFANA_IMAGE}
    container_name: grafana
    ports:
      - "${GRAFANA_PORT}:3000"
    volumes:
      - grafana_data:/var/lib/grafana
      - ./config/dashboards:/etc/grafana/provisioning/dashboards  # Mount dashboard config
      - ./config/datasources:/etc/grafana/provisioning/datasources  # Mount data sources (to be created next)
    networks:
      - ${DOCKER_NETWORK}
    restart: unless-stopped
```

2. **Add dashboard provisioning file:**

Create a file:

`fctnursing-hrms-devops-attendance-stack/config/dashboards/dashboard.yml`

```bash
# config/dashboards/dashboard.yml
apiVersion: 1

providers:
  - name: 'Attendance Dashboard'
    folder: 'HRMS Monitoring'
    type: file
    options:
      path: /etc/grafana/provisioning/dashboards
```

3. Add datasource provisioning file:

Create another file:

`fctnursing-hrms-devops-attendance-stack/config/datasources/datasource.yml`

```bash
# =======================================
# Grafana Datasource Configuration File
# =======================================
# Purpose: Provision Grafana with Prometheus as the default data source.

apiVersion: 1  # Grafana API version for provisioning

datasources:
  - name: Prometheus                          # Data source name as it will appear in Grafana UI
    type: prometheus                          # Type of data source
    access: proxy                             # 'proxy' means Grafana queries Prometheus server directly
    orgId: 1                                  # Default organization ID
    url: http://prometheus:9090               # Internal service name and port (as defined in docker-compose)
    isDefault: true                           # Set this Prometheus instance as the default data source
    version: 1                                # Data source version
    editable: true                            # Allow users to edit this data source from the UI
```

### ✅ Explanation of url:

- We use http://prometheus:9090 because:

  - prometheus is the Docker Compose service name.

  - 9090 is the container port, not the host port (${PROMETHEUS_PORT}), and that’s correct for container-to-container communication.
  - 

4. Attendance Dashboard JSON

`fctnursing-hrms-devops-attendance-stack/config/dashboards/attendance-dashboard.json`

```bash
{
  // Root level object that contains both dashboard details and how Grafana should handle it
  "dashboard": {
    // Grafana assigns an ID automatically; leave as null when creating new dashboards
    "id": null,
    
    // UID can be used for referencing this dashboard in URLs; null means Grafana will generate it
    "uid": null,

    // Title displayed on the dashboard UI
    "title": "Attendance Monitoring",

    // Optional tags for searching and filtering dashboards
    "tags": ["attendance", "monitoring", "jenkins", "timetrex"],

    // Sets the timezone for data display (browser = client time)
    "timezone": "browser",

    // List of panels (charts/graphs/visual elements) included in the dashboard
    "panels": [

      // ─────────── PANEL 1: Jenkins CPU Usage ───────────
      {
        // Type of visualization: 'graph' means line/time series graph
        "type": "graph",

        // Title shown above this graph
        "title": "CPU Usage - Jenkins",

        // Query that fetches CPU usage metric for Jenkins from Prometheus
        "targets": [
          {
            // Prometheus query expression for Jenkins CPU usage
            "expr": "rate(process_cpu_seconds_total{job=\"jenkins\"}[2m])",

            // Label shown in the graph legend
            "legendFormat": "CPU",

            // Reference ID for Grafana's internal use
            "refId": "A"
          }
        ],

        // Data source for this graph (must match a configured Prometheus source)
        "datasource": "Prometheus",

        // Unique ID for this panel (within this dashboard)
        "id": 1,

        // Grid position and size: height, width, and X/Y placement on dashboard grid
        "gridPos": {
          "h": 9,   // height (in grid units)
          "w": 12,  // width
          "x": 0,   // horizontal position (starts from left)
          "y": 0    // vertical position (starts from top)
        }
      },

      // ─────────── PANEL 2: TimeTrex CPU Usage ───────────
      {
        "type": "graph",
        "title": "CPU Usage - TimeTrex",
        "targets": [
          {
            "expr": "rate(process_cpu_seconds_total{job=\"timetrex\"}[2m])",
            "legendFormat": "CPU",
            "refId": "B"
          }
        ],
        "datasource": "Prometheus",
        "id": 2,
        "gridPos": {
          "h": 9,
          "w": 12,
          "x": 12,  // Placed to the right of Jenkins graph
          "y": 0
        }
      }
    ],

    // Schema version used by Grafana — should match your Grafana version
    "schemaVersion": 36,

    // Revision number of this dashboard
    "version": 1,

    // How often Grafana should auto-refresh data for this dashboard (every 5 seconds)
    "refresh": "5s"
  },

  // If a dashboard with the same title exists, overwrite it during import
  "overwrite": true
}
```

### 📝 Explanation:

- `title:` "`Attendance Monitoring`" — this is the name shown in the Grafana UI.
- Two graph panels: one for Jenkins CPU usage and one for TimeTrex.
- Metrics are pulled using:
  - `process_cpu_seconds_total{job="jenkins"}` and
  - `process_cpu_seconds_total{job="timetrex"}`.
- These metrics work as long **as node_exporter or the relevant exporter is enabled on those containers or within their base image** (e.g., Jenkins already exposes JVM metrics).


## 📦 Step 8: Docker Compose to Run Services

With everything set up, we can now run all the services using Docker Compose.

1. Navigate to your project directory:

```bash
cd fctnursing-hrms-devops-attendance-stack
```

2. Start all services using Docker Compose:

```bash
docker-compose --env-file .env up -d
```

**This command will:**

- Pull the images for TimeTrex, Prometheus, Grafana, and Jenkins.

- Start the containers for each service in the background (`-d` means detached mode).

## ⚠️ Error: Docker Compose Fails with Undefined Network Referenced in Services

When running:

```bash
docker-compose --env-file .env up -d
```

I saw this error:

```
service "timetrex" refers to undefined network hrms_stack_net: invalid compose project
```

### 🔍 Root Cause:

- I attempted to use **environment variable substitution** for a **top-level networks key** like this:

```bash
networks:
  ${DOCKER_NETWORK}:
    driver: bridge
```

However, **Docker Compose** does not support **environment variable substitution** at the **top level** for `networks`, `volumes`, or `secrets`. This restriction means:

- I can use `${DOCKER_NETWORK}` inside a service definition, like so:

```bash
services:
  timetrex:
    networks:
      - ${DOCKER_NETWORK}  # ✅ This is supported
```

- But you cannot define the network at the **top level** using a variable:

```bash
networks:
  ${DOCKER_NETWORK}:  # ❌ This will not work
    driver: bridge
```

Because the top-level network was not actually created (due to invalid syntax), all services referencing that network failed.

## ✅ Solution:

Replace the environment variable with the actual network name (`hardcoded`) in the `networks`: block:

```bash
networks:
  hrms_stack_net:     # ✅ Use plain text instead of ${DOCKER_NETWORK}
    driver: bridge
```

Then reference it in your services as usual — you can still use ${DOCKER_NETWORK} there if desired, as long as the network is defined using its actual name.

### 🧠 Why This Happens:

Docker Compose parses the networks: and volumes: sections before evaluating environment variables, so it cannot substitute variables in these top-level keys.

![](./docs/img/10.docker-compose-running.png)

![](./docs/img/11.resources-pulled.png)

![](./docs/img/12.running-containers.png)

### 🛠️ Alternative Workaround (Optional):

If you really need dynamic network names, consider using a script (e.g., bash or Python) to generate your `docker-compose.yml` dynamically by replacing placeholders before running docker-compose.

**Here's a simple and practical Bash script that reads a `.env` file and generates a new `docker-compose.generated.yml` file by replacing the `${VARIABLE}` placeholders with actual values from `.env`.**


### ✅ 1. Script: `generate_compose.sh`

```bash
#!/bin/bash

# Load variables from .env into current shell
export $(grep -v '^#' .env | xargs)

# Input and output file
TEMPLATE_FILE="docker-compose.yml"
OUTPUT_FILE="docker-compose.generated.yml"

# Create a new file with substituted variables
envsubst < "$TEMPLATE_FILE" > "$OUTPUT_FILE"

echo "✅ Generated $OUTPUT_FILE with values from .env"
```

### ✅ 2. Usage Instructions

- Make the script executable:
  
  ```
  chmod +x generate_compose.sh
  ```

- Run it:
  
  ```
  ./generate_compose.sh
  ```

- Then deploy using:
  
  ```
  docker-compose -f docker-compose.generated.yml up -d
  ```

### 🔄 What It Does

- It uses envsubst (standard UNIX tool) to substitute environment variables with their values from .env.

-  preprocesses your Compose file, removing the need for Compose to handle dynamic top-level fields like networks.
-  

### 🧪 Example Before & After

Original `docker-compose.yml`

```bash
networks:
  ${DOCKER_NETWORK}:
    driver: bridge

services:
  timetrex:
    image: ${TIMETREX_IMAGE}
    networks:
      - ${DOCKER_NETWORK}
```

- Generated `docker-compose.generated.yml`

```bash
networks:
  hrms_stack_net:
    driver: bridge

services:
  timetrex:
    image: ghcr.io/org/timetrex:latest
    networks:
      - hrms_stack_net
```

 Here's an enhanced version of the script that does:

- 🗂 Backs up your existing docker-compose.generated.yml if it exists.

- ⚙️ Generates a new one by replacing environment variables.

- 🚀 Automatically runs docker-compose up -d using the generated file.

✅ Final Script: `generate_and_deploy.sh`

```bash
#!/bin/bash

# Exit immediately if a command exits with a non-zero status
set -e

# Load variables from .env into current shell
export $(grep -v '^#' .env | xargs)

# Define input/output files
TEMPLATE_FILE="docker-compose.yml"
OUTPUT_FILE="docker-compose.generated.yml"
BACKUP_FILE="docker-compose.generated.bak.yml"

# Backup the old generated file if it exists
if [ -f "$OUTPUT_FILE" ]; then
  cp "$OUTPUT_FILE" "$BACKUP_FILE"
  echo "📦 Backup created: $BACKUP_FILE"
fi

# Generate new docker-compose file with substituted variables
envsubst < "$TEMPLATE_FILE" > "$OUTPUT_FILE"
echo "✅ Generated: $OUTPUT_FILE"

# Deploy the stack using the generated file
docker-compose -f "$OUTPUT_FILE" up -d
echo "🚀 Docker Compose stack is up and running using: $OUTPUT_FILE"

```



## 🔍 STEP 9: Access Each Service

- TimeTrex	http://localhost:8085
- Prometheus	http://localhost:9090
- Grafana	http://localhost:3100
- Jenkins	http://localhost:8180

