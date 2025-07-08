# Ansible Playbook for Checkmk Agent on Linux

[![Ansible Lint](https://github.com/MarkDevelo/checkmk-agent-ansible-linux/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/MarkDevelo/checkmk-agent-ansible-linux/actions/workflows/ansible-lint.yml)

A robust Ansible playbook to automate the installation of the Checkmk monitoring agent on **Debian and Ubuntu** servers.

This repository provides two methods for deployment:

1.  **Professional Project (main branch):** A secure, scalable structure using Ansible Vault for secrets and an inventory file for managing multiple hosts. **This is the recommended approach.**
2.  **Interactive Script (interactive-version branch):** A simple, single-file script that prompts for all inputs. Ideal for quick, one-off deployments.

---

## Method 1: Professional Project (Recommended)

This method is ideal for managing multiple servers in a repeatable and secure way.

### Features

-   **Secure by Default:** Uses **Ansible Vault** to manage all sensitive passwords.
-   **Scalable:** Manage hundreds of servers easily through the `inventory.ini` file.
-   **Idempotent:** Safe to run multiple times without causing issues.
-   **Firewall Configuration:** Automatically opens port 6556 using `ufw`.

### Setup & Configuration Guide

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/MarkDevelo/checkmk-agent-ansible-linux.git
    cd checkmk-agent-ansible-linux
    ```

2.  **Install Dependencies:**
    ```bash
    ansible-galaxy install -r requirements.yml
    ```

3.  **Configure the Agent Version:**
    -   Place your Checkmk agent `.deb` file into the `files/` directory.
    -   Open `vars/config.yml` and set the `agent_deb_file` variable to match the **exact filename** of your agent.

4.  **Configure Your Target Servers:**
    -   Open `inventory.ini` and list your server IPs under the `[checkmk_linux_agents]` group.
    -   Update the `ansible_user` variable with a user that has `sudo` access.

5.  **Create Your Encrypted Secrets File:**
    -   Run `ansible-vault create vars/secrets.yml.vault`. You will be prompted to create a vault password. **Remember this password!**
    -   Paste the following into the editor, add your real credentials, then save the file:
        ```yaml
        ansible_password: "YourSshPassword"
        ansible_become_password: "YourSudoPassword"
        ```

### How to Run the Playbook

Execute the playbook with the following command. It will ask for your vault password.

```bash
ansible-playbook -i inventory.ini install_checkmk_agent.yml --ask-vault-pass
```

---

## Method 2: Quick Interactive Script

This method is perfect for quickly installing the agent on a single server without setting up an inventory or vault.

### 1. Switch to the Interactive Branch

First, switch to the `interactive-version` branch to access the script.

```bash
git checkout interactive-version
```

### 2. Run the Interactive Playbook

Execute the interactive playbook directly. The script will prompt you for all the necessary information (target IP, username, password, and the path to your `.deb` file).

```bash
ansible-playbook install_checkmk_agent_interactive.yml
```

---

## License

This project is licensed under the [MIT License](LICENSE).
