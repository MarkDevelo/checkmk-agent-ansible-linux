# Ansible Playbook for Checkmk Agent on Linux

[![Ansible Lint](https://github.com/your-username/checkmk-agent-ansible-linux/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/your-username/checkmk-agent-ansible-linux/actions/workflows/ansible-lint.yml)

A robust and professional Ansible playbook to automate the installation of the Checkmk monitoring agent on **Debian and Ubuntu** servers.

This project is designed to be idempotent, secure, and easily configurable for any environment. It follows Ansible best practices for project structure and security.

## Features

-   **Linux Focused:** Optimized specifically for Debian-based systems like Debian and Ubuntu.
-   **Idempotent:** Safe to run multiple times without causing issues.
-   **Secure by Default:** Uses **Ansible Vault** to manage all sensitive passwords securely.
-   **Firewall Configuration:** Automatically opens the required port (6556/tcp) using `ufw`.
-   **Clean & Standard Structure:** Follows Ansible best practices for easy management.

---

## Setup & Configuration Guide

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/checkmk-agent-ansible-linux.git
cd checkmk-agent-ansible-linux
```

### 2. Install Dependencies

This project depends on the `community.general` Ansible collection.

```bash
ansible-galaxy install -r requirements.yml
```

### 3. Configure the Agent Version

This is the most important step for adapting the playbook to your needs.

1.  **Place your Agent File:** Copy your agent installer (e.g., `check-mk-agent-2.4.0.deb`) into the `files/` directory.
2.  **Update the Config File:** Open the `vars/config.yml` file and change the `agent_deb_file` variable to match the **exact filename** of the agent you just added.

    ```yaml
    # vars/config.yml
    agent_deb_file: "check-mk-agent-2.4.0.deb"
    ```

### 4. Configure Your Target Servers

Open the `inventory.ini` file:
-   List all your target server IPs or hostnames under the `[checkmk_linux_agents]` group.
-   Set the `ansible_user` variable to a user that has `sudo` access on your remote servers.

### 5. Create Your Encrypted Secrets File

This project requires you to store passwords securely using Ansible Vault.

1.  Run the vault creation command. You will be prompted to create a new password for the vault. **Remember this vault password!**
    ```bash
    ansible-vault create vars/secrets.yml.vault
    ```

2.  The command will open a text editor. Paste the following into the editor, replacing the placeholders with your actual credentials, then **save and close the file**.
    ```yaml
    # Password for the user defined in inventory.ini
    ansible_password: "YourSshPassword"

    # Sudo password for the user
    ansible_become_password: "YourSudoPassword"
    ```

---

## How to Run the Playbook

Execute the playbook using the following command. It will prompt you for the vault password you just created.

```bash
ansible-playbook -i inventory.ini install_checkmk_agent.yml --ask-vault-pass
```

## License

This project is licensed under the [MIT License](LICENSE).
