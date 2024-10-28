# SonarQube Installation Guide

This guide will walk you through installing and configuring SonarQube with PostgreSQL on Ubuntu 22.04 or newer.

## Prerequisites

- A Virtual Machine running Ubuntu 22.04 or newer
- PostgreSQL 17

## Step 1: System Update

Before installing any packages, ensure your system is up to date:

```bash
sudo apt update
sudo apt upgrade
```
# SonarQube Installation Guide

This guide walks you through installing and configuring PostgreSQL 15 for SonarQube on Ubuntu 22.04 or newer.

## Step 2: Install PostgreSQL 17

1. **Add PostgreSQL repository to your sources list:**

    ```bash
    sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    ```

2. **Add the PostgreSQL signing key:**

    ```bash
    wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
    ```

3. **Update your package list and install PostgreSQL:**

    ```bash
    sudo apt update
    sudo apt install postgresql-17 postgresql-contrib
    ```

4. **Enable PostgreSQL to start on boot:**

    ```bash
    sudo systemctl enable postgresql
    ```

## Step 3: Set Up PostgreSQL for SonarQube

1. **Set a password for the `postgres` user:**

    ```bash
    sudo passwd postgres
    ```

2. **Switch to the `postgres` user:**

    ```bash
    su - postgres
    ```

3. **Create a new user for SonarQube:**

    ```bash
    createuser sonar
    ```

4. **Access the PostgreSQL interactive shell:**

    ```bash
    psql
    ```

5. **Set a password for the `sonar` user:**

    ```bash
    ALTER USER sonar WITH ENCRYPTED password 'sonar';
    ```

6. **Create a database for SonarQube owned by the `sonar` user:**

    ```bash
    CREATE DATABASE sonarqube OWNER sonar;
    ```

7. **Grant all privileges on the SonarQube database to the `sonar` user:**

    ```bash
    GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonar;
    ```

8. **Exit the PostgreSQL shell:**

    ```bash
    \q
    ```

9. **Exit the `postgres` user:**

    ```bash
    exit
    ```

---

Your PostgreSQL setup is now complete. You can now proceed with the SonarQube installation!
## Additional Notes

- **Make sure that PostgreSQL is running** after installation by checking the status:

    ```bash
    sudo systemctl status postgresql
    ```

- You can **restart PostgreSQL** with:

    ```bash
    sudo systemctl restart postgresql
    ```

---
## Install Java 17

To install Java 17, follow these steps:

1. **Switch to root user:**

    ```bash
    sudo bash
    ```

2. **Install necessary packages:**

    ```bash
    apt install -y wget apt-transport-https
    ```

3. **Create directory for storing the GPG key:**

    ```bash
    mkdir -p /etc/apt/keyrings
    ```

4. **Download and add the Adoptium GPG key:**

    ```bash
    wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
    ```

5. **Add the Adoptium repository:**

    ```bash
    echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
    ```

6. **Update the package list:**

    ```bash
    apt update
    ```

7. **Install the Temurin 17 JDK:**

    ```bash
    apt install temurin-17-jdk
    ```

8. **Configure the default Java version (if necessary):**

    ```bash
    update-alternatives --config java
    ```

9. **Verify the installation by checking the Java version:**

    ```bash
    /usr/bin/java --version
    ```

---

You have successfully installed Java 17!

---
## Increase Limits for SonarQube

To ensure that SonarQube can operate efficiently, you need to adjust the user limits for the `sonarqube` user. This involves modifying the `limits.conf` file to set the maximum number of open files and processes.

### Step 1: Update `limits.conf`

1. Open the `limits.conf` file:

    ```bash
    sudo vim /etc/security/limits.conf
    ```

2. Paste the following values at the bottom of the file:

    ```
    sonarqube   -   nofile   65536
    sonarqube   -   nproc    4096
    ```
## Resource Limits for SonarQube

To optimize SonarQube's performance and reliability, the following resource limits are configured for the `sonarqube` user:

1. **Open File Descriptors**:
   - **Line**: `sonarqube - nofile 65536`
   - **Explanation**: This line increases the maximum number of open file descriptors to **65,536**. This is important for applications like SonarQube that may handle many files simultaneously, including logs, configuration files, and temporary files. A higher limit ensures that SonarQube can operate without running into errors related to too many open files, especially during peak usage times.

2. **Process Limits**:
   - **Line**: `sonarqube - nproc 4096`
   - **Explanation**: This line increases the maximum number of processes that the `sonarqube` user can create to **4,096**. This is essential for performance, especially under high load conditions. By allowing more processes, SonarQube can manage multiple tasks and analyses concurrently, improving overall efficiency and responsiveness.

By setting these limits, you enhance SonarQube's ability to function smoothly and effectively in a high-demand environment.
### Step 2: Update `sysctl.conf`
- To optimize the performance of SonarQube, it is crucial to configure the kernel parameter `vm.max_map_count`. This setting defines the maximum number of memory 
  map areas a process may have.
---
#### Why Update `sysctl.conf`?

1. **Memory Mapping**:
   - The `vm.max_map_count` parameter controls how many memory map areas a single process can use. Each memory-mapped file or shared memory segment counts toward this limit. SonarQube, particularly in large deployments, may require a significant number of memory mappings to operate efficiently.

2. **High Load Handling**:
   - During high-load conditions, such as when running analyses on multiple projects simultaneously, SonarQube may need to map a substantial number of files and data structures into memory. A higher limit allows it to handle more concurrent operations without encountering memory mapping errors.

3. **Performance Optimization**:
   - Setting `vm.max_map_count` to **262,144** ensures that SonarQube can efficiently utilize system resources, leading to improved performance and responsiveness, especially in scenarios with many projects and large codebases.

4. **Error Prevention**:
   - Insufficient mapping limits can lead to errors during execution, such as `Too many open files` or memory mapping failures. By increasing this limit, you reduce the risk of such errors occurring.
---
### Configuration Steps
1. Open the `sysctl.conf` file:

    ```bash
    sudo vim /etc/sysctl.conf
    ```

2. Paste the following value at the bottom of the file:

    ```
    vm.max_map_count = 262144
    ```

### Step 3: Reboot the System

To apply the changes, reboot the system:

```bash
sudo reboot
```
## Install SonarQube

Follow these steps to install and configure SonarQube on your server.

### Step 1: Download and Extract SonarQube

1. **Download SonarQube:**

    ```bash
    sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
    ```

2. **Install `unzip` and extract the package:**

    ```bash
    sudo apt install unzip
    sudo unzip sonarqube-9.9.0.65466.zip -d /opt
    ```

3. **Move SonarQube to a new directory:**

    ```bash
    sudo mv /opt/sonarqube-9.9.0.65466 /opt/sonarqube
    ```

4. **Create a SonarQube user and group:**

    ```bash
    sudo groupadd sonar
    sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
    ```

5. **Set ownership of the SonarQube directory:**

    ```bash
    sudo chown sonar:sonar /opt/sonarqube -R
    ```

### Step 2: Update SonarQube Properties with Database Credentials

1. Open the SonarQube configuration file:

    ```bash
    sudo vim /opt/sonarqube/conf/sonar.properties
    ```

2. Find and replace the following lines (or add them if necessary):

    ```ini
    sonar.jdbc.username=sonar
    sonar.jdbc.password=sonar
    sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
    ```

### Step 3: Create a Systemd Service for SonarQube

1. Open a new service file for SonarQube:

    ```bash
    sudo vim /etc/systemd/system/sonar.service
    ```

2. Paste the following configuration into the file:

    ```ini
    [Unit]
    Description=SonarQube service
    After=syslog.target network.target

    [Service]
    Type=forking

    ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
    ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

    User=sonar
    Group=sonar
    Restart=always

    LimitNOFILE=65536
    LimitNPROC=4096

    [Install]
    WantedBy=multi-user.target
    ```

### Step 4: Start and Enable the SonarQube Service

1. **Start the SonarQube service:**

    ```bash
    sudo systemctl start sonar
    ```

2. **Enable SonarQube to start at boot:**

    ```bash
    sudo systemctl enable sonar
    ```

3. **Check the status of SonarQube:**

    ```bash
    sudo systemctl status sonar
    ```

4. **Check the SonarQube logs to verify startup:**

    ```bash
    sudo tail -f /opt/sonarqube/logs/sonar.log
    ```

### Step 5: Access the SonarQube UI

Once SonarQube is up and running, you can access the SonarQube UI in your browser:
## Access the SonarQube UI

Once SonarQube is up and running, you can access the SonarQube UI in your browser:

```yaml
http://<IP>:9000
```
