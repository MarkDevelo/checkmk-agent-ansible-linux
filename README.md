# Ansible Playbook for Checkmk Agent on Linux

A robust Ansible playbook to automate the installation of the Checkmk monitoring agent on **Debian and Ubuntu** servers.

This repository provides two distinct methods for deployment, allowing you to choose the best fit for your workflow.

1.  **Professional Project (main branch):** A secure, scalable structure using Ansible Vault for secrets and an inventory file for managing multiple hosts. **This is the recommended approach for production environments.**
2.  **Interactive Script (interactive-version branch):** A simple, single-file script that prompts for all inputs. Ideal for quick, one-off deployments or testing.

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
    This project depends on the `community.general` Ansible collection.
    ```bash
    ansible-galaxy install -r requirements.yml
    ```

3.  **Configure the Agent Version:**
    This is the most important step for adapting the playbook to your needs.
    -   Place your Checkmk agent `.deb` file into the `files/` directory.
    -   Open `vars/config.yml` and set the `agent_deb_file` variable to match the **exact filename** of your agent.

    ```yaml
    # Example: vars/config.yml
    agent_deb_file: "check-mk-agent-2.4.0p1.deb"
    ```

4.  **Configure Your Target Servers:**
    -   Open `inventory.ini` and list your server IPs or hostnames under the `[checkmk_linux_agents]` group.
    -   Update the `ansible_user` variable with a user that has `sudo` access.

5.  **Create Your Encrypted Secrets File:**
    -   Run `ansible-vault create vars/secrets.yml.vault`. You will be prompted to create a vault password. **Remember this vault password!**
    -   Paste the following into the editor, add your real credentials, then save and close the file:
        ```yaml
        # Password for the SSH user defined in inventory.ini
        ansible_password: "YourSshPassword"

        # Sudo password for that user
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

To access the script, you must switch to the `interactive-version` branch.

```bash
# Fetch the latest branches from the remote repository
git fetch origin

# Switch to the interactive version branch
git checkout interactive-version
```

### 2. Important: Check the Agent Installer Path

The interactive script prompts you for the location of your Checkmk `.deb` file. It has a default path set for convenience.

```yaml
# Snippet from install_checkmk_agent_interactive.yml
...
    - name: linux_package
      prompt: "Path to Linux .deb package on this machine"
      default: "/home/steve/check-mk-agent.deb" # <-- This default path
      private: no
...
```

**Before running the script, a new user should be aware of two options:**

*   **Option A (Recommended):** Simply run the script. When it prompts you for the path, **manually type the correct, full path** to where your `.deb` file is located on your machine.

*   **Option B (For Convenience):** **Edit the `install_checkmk_agent_interactive.yml` file** and change the `default:` path to a location you use often. This saves you from typing it every time.

### 3. Run the Interactive Playbook

Execute the playbook directly from your terminal. The script will ask for all required information.

```bash
# This command assumes you are in the project's root directory
ansible-playbook install_checkmk_agent_interactive.yml
```

The playbook will then connect to the target server and perform the installation.

---

## License

This project is licensed under the [MIT License](LICENSE).
