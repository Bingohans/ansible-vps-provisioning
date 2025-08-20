# Automated VPS Provisioning with Ansible

![Project Status](https://img.shields.io/badge/status-complete-green)
![Ansible Version](https://img.shields.io/badge/Ansible-2.16-blue)

---

## Project Overview

This project uses Ansible to automate the provisioning of essential observability agents on multiple remote servers. The playbook is designed to be idempotent and ensures that target servers are consistently configured with a baseline for monitoring and log aggregation.

The primary goal is to deploy **Node Exporter** for Prometheus metrics and **Promtail** for Grafana Loki log shipping, establishing a foundation for a centralized observability stack. This demonstrates core DevOps principles of automation, configuration management, and Infrastructure as Code (IaC).

---

## Architecture

The setup involves a single **Ansible Control Node** (a Raspberry Pi) which provisions multiple **Managed Nodes** (Cloud VPS instances).

* **Control Node (Raspberry Pi):** Runs the Ansible playbook.
* **Managed Nodes (VPS Servers):** The target servers that are configured by Ansible.

All communication happens securely over SSH using key-based authentication.

---

## Features Automated

The playbook performs the following actions on all target servers:

* Updates the APT package cache.
* Installs `prometheus-node-exporter` from standard repositories.
* Downloads and installs the `promtail` binary directly from GitHub releases.
* Creates a `systemd` service for Promtail to ensure it runs on boot.
* Configures a basic Promtail setup to scrape system logs from `/var/log/`.
* Ensures both `node_exporter` and `promtail` services are running.

---

## How to Use

1.  **Clone the Repository:**
    ```bash
    git clone <your-repo-url>
    cd <your-repo-name>
    ```

2.  **Configure the Inventory:**
    Create an `inventory.ini` file and add your target servers. Specify the user and port if they are non-default.
    ```ini
    [vps_servers]
    vps1 ansible_host=YOUR_VPS1_IP ansible_user=your_user ansible_port=22
    vps2 ansible_host=YOUR_VPS2_IP ansible_user=your_user ansible_port=2251
    ```

3.  **Run the Playbook:**
    Execute the playbook from your control node.
    ```bash
    ansible-playbook -i inventory.ini setup-vps.yml
    ```

---

## Challenges & Solutions

* **Challenge:** The playbook failed with a Python `HTTPSConnection` error when trying to download the Promtail binary. The error persisted even after updating `python3-urllib3` via `apt`.
    * **Solution:** The root cause was a Python dependency conflict on the control node. The system's Python environment was interfering with Ansible's requirements. The problem was solved by creating an isolated **Python virtual environment** (`venv`), installing Ansible and its dependencies cleanly inside it, and running the playbook from within this environment. This is a best-practice solution for running Python-based tools.

---

## Configuration Files Included

* **`setup-vps.yml`:** The main Ansible playbook containing all tasks.
* **`inventory.ini.example`:** An example inventory file showing how to define hosts.
