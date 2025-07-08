# Ansible Playbook for Checkmk Agent on Linux

A robust Ansible playbook to automate the installation of the Checkmk monitoring agent on **Debian and Ubuntu** servers.

This repository provides two distinct methods for deployment, allowing you to choose the best fit for your workflow.

1.  **Method 1: Structured Project (Recommended for multiple hosts)**
    A scalable structure using Ansible Vault for secrets and an inventory file.
2.  **Method 2: Quick Interactive Script (Recommended for one-off tasks)**
    A simple, single-file script that prompts for all inputs.

---

## Method 1: Structured Project (Scalable and Secure)

This method is ideal for managing multiple servers in a repeatable and secure way. It uses the files located on the `main` branch of this repository.

### Features

-   **Secure:** Uses **Ansible Vault** to manage all sensitive passwords.
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
    -   Run `ansible-vault create vars/secrets.yml.vault`. You will be prompted to create a vault password. **Remember this password!**
    -   Paste the following into the editor, add your real credentials, then save and close the file:
        ```yaml
        # Password for the SSH user defined in inventory.ini
        ansible_password: "YourSshPassword"

        # Sudo password for that user
        ansible_become_password: "YourSudoPassword"
        ```

### How to Run

Execute the playbook with the following command. It will ask for your vault password.

```bash
ansible-playbook -i inventory.ini install_checkmk_agent.yml --ask-vault-pass
```

---

## Method 2: Quick Interactive Script (One-off Deployments)

This method is perfect for quickly installing the agent on a single server. You do not need to clone the repository or use Git branches. You only need the single script file.

### Setup & Execution Guide

1.  **Download the Interactive Script:**
    Download the playbook directly from the `interactive-version` branch using `curl` or `wget`.

    ```bash
    # Using curl:
    curl -O https://raw.githubusercontent.com/MarkDevelo/checkmk-agent-ansible-linux/interactive-version/install_checkmk_agent_interactive.yml
    
    # Or using wget:
    # wget https://raw.githubusercontent.com/MarkDevelo/checkmk-agent-ansible-linux/interactive-version/install_checkmk_agent_interactive.yml
    ```

2.  **Prepare Your Agent Installer:**
    Make sure you have the Checkmk agent `.deb` file downloaded on the same machine where you are running the script.

3.  **Run the Playbook:**
    Execute the downloaded playbook using `ansible-playbook`.

    ```bash
    ansible-playbook install_checkmk_agent_interactive.yml
    ```

4.  **Follow the Prompts:**
    The script will interactively ask you for:
    -   The target server IP address.
    -   The remote username (must have sudo access).
    -   The password for that user (used for both SSH and sudo).
    -   **The full path to the `.deb` file on your local machine.**

    **Note on the File Path:** The script has a default path (`/home/steve/check-mk-agent.deb`). You must manually type the correct path to your `.deb` file when prompted, or edit the YAML file to change the default before running it.

---

## License

This project is licensed under the [MIT License](LICENSE).
