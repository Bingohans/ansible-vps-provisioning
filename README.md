# Automated VPS Provisioning with Ansible and Docker

![Project Status](https://img.shields.io/badge/status-complete-green)
![Ansible Version](https://img.shields.io/badge/Ansible-2.16-blue)
![Docker Version](https://img.shields.io/badge/Docker-24.0-blue)

---

## Project Overview

This project uses Ansible to automate the provisioning and management of essential observability agents (**Node Exporter** and **Promtail**) on multiple remote servers. The agents are deployed as **Docker containers** using Docker Compose, ensuring a consistent, isolated, and easily manageable setup.

The playbook is designed to be idempotent and establishes a foundation for a centralized observability stack (Prometheus and Loki). This demonstrates core DevOps principles of automation, containerization, configuration management, and Infrastructure as Code (IaC).

---

## Architecture

The setup involves a single **Ansible Control Node** (a Raspberry Pi) which provisions multiple **Managed Nodes** (Cloud VPS instances). The agents run as containerized services on the managed nodes.

* **Control Node (Raspberry Pi):** Runs the Ansible playbooks.
* **Managed Nodes (VPS Servers):** The target servers that are configured by Ansible to run the monitoring agents in Docker.

All communication happens securely over SSH using key-based authentication.

---

## Project Evolution: From Bare-Metal to Containers

This repository demonstrates a deliberate migration from a traditional to a modern deployment strategy, showcasing a full application lifecycle management approach.

Initially, the agents were installed directly on the host operating system ("bare-metal"). While functional, this approach presented challenges with dependency management and scalability. The project was then evolved to deploy the agents as Docker containers, which is the current and recommended setup.

* **`deploy-docker-agents.yml` (Recommended):** The primary playbook that deploys the observability agents as isolated Docker containers for a modern, scalable, and consistent setup.
* **`cleanup-bare-metal.yml`:** A utility playbook designed to uninstall the previous bare-metal versions of these agents, facilitating a clean migration to the containerized approach.

---

## How to Use

1.  **Clone the Repository:**
    ```bash
    git clone <your-repo-url>
    cd <your-repo-name>
    ```

2.  **Configure the Inventory:**
    Create an `inventory.ini` file and add your target servers.
    ```ini
    [vps_servers]
    vps1 ansible_host=YOUR_VPS1_IP ansible_user=your_user
    vps2 ansible_host=YOUR_VPS2_IP ansible_user=your_user
    ```

3.  **Uninstall previous versions (if applicable):**
    If you previously installed the agents directly on the host, run the cleanup playbook to ensure a clean state.
    ```bash
    ansible-playbook -i inventory.ini cleanup-bare-metal.yml
    ```

4.  **Deploy the Dockerized Agents:**
    Execute the main playbook to deploy the services.
    ```bash
    ansible-playbook -i inventory.ini deploy-docker-agents.yml
    ```

---

## Challenges & Solutions

This project involved overcoming challenges in both the initial bare-metal phase and the migration to Docker.

* **Challenge (Bare-Metal):** The initial Ansible playbook failed with a Python `HTTPSConnection` error on the control node due to dependency conflicts within the system's Python environment.
    * **Solution:** The problem was solved by creating an isolated **Python virtual environment** (`venv`) and installing Ansible cleanly inside it. This is a best-practice for running Python-based tools and ensures a stable, predictable environment for automation tasks.

* **Challenge (Migration):** Managing application dependencies and ensuring consistent environments across multiple servers with a bare-metal approach proved to be brittle and hard to scale.
    * **Solution:** Migrated the services from bare-metal installation to **Docker containers**. This encapsulates all dependencies, simplifies deployment, and makes the setup far more portable and scalable. The entire container configuration is now managed by Ansible, demonstrating a layered automation approach.

---

## Configuration Files Included

* **`deploy-docker-agents.yml`:** The main Ansible playbook for deploying agents via Docker.
* **`cleanup-bare-metal.yml`:** The utility playbook for cleaning up previous installations.
* **`inventory.ini.example`:** An example inventory file.
