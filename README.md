# Ansible_aws_ec2_creation_and_shutdown
Ansible project using roles and Ansible Vault to provision and manage multi-OS AWS EC2 instances (Ubuntu &amp; Amazon Linux) with automated state control.
# AWS EC2 Multi-OS Provisioning & Management with Ansible

This repository contains an Ansible automation framework designed to provision, configure, and manage cloud infrastructure on AWS (`eu-north-1` region). Using a modular architecture with **Ansible Roles**, the project orchestrates a heterogeneous environment consisting of both Ubuntu and Amazon Linux instances while ensuring sensitive credentials remain secure via **Ansible Vault**.

## 🚀 Features

- **Multi-OS Orchestration:** Provisions 3 EC2 instances simultaneously (2 Ubuntu Nodes, 1 Amazon Linux Node).
- **Passwordless SSH Authentication:** Automatically injects and configures public keys during the infrastructure lifecycle, enabling seamless, passwordless management from the control node.
- **Modular Architecture:** Utilizes structured Ansible Roles to cleanly separate infrastructure provisioning from target configuration tasks.
- **Secure Secret Management:** Integrates Ansible Vault to encrypt and safeguard sensitive AWS access keys and session tokens.
- **Dynamic State Management:** Includes a dedicated automation playbook to safely target and shut down specific infrastructure components (e.g., stopping only the Ubuntu environments).
- **Dynamic Inventory Integration:** Bridges the gap between localhost provisioning and remote target execution seamlessly using runtime grouping mechanisms.

---

## 📂 Project Structure

```text
├── ec2_create.yaml           # Master playbook for creating and configuration the ec2 instances
├── ec2_stop.yaml             # Playbook to safely stop Ubuntu instances which have "Debian os_family"
├── group_vars/
│   └── all/
│       └── vault.yml        # Encrypted AWS credentials (Ansible Vault)
└── roles/
    └── ec2s/
        ├── vars/
        │   └── main.yml     # Infrastructure layout, target OS mappings, and AMI paths
        └── tasks/
            ├── main.yml     # Core task orchestration engine
            
```

---

## 🛠️ Prerequisites

Before running the playbooks, ensure your control node has the following requirements configured:

1. **Ansible & Collections:**
   ```bash
   ansible-galaxy collection install amazon.aws
   ```
2. **Python Dependencies:**
   First, you need python3
   ```bash
   apt install python3
   ```
   Then install Boto3
   ```bash
   pip install boto3 || apt install python3-boto3
   ```

---

## 🔑 Configuration & Security

### Passwordless SSH Setup
To enable passwordless authentication, ensure your local SSH public key (`~/.ssh/id_rsa.pub` or similar) is correctly mapped to the `key_name` parameter within your configuration variables or the authorized_keys in (`~/.ssh/authorized_keys) in all managed nodes. Ansible leverages AWS key pairs to authorize access automatically without prompt challenges.

### Editing Encrypted Secrets
Sensitive infrastructure variables (such as `aws_access_key` and `aws_secret_key`) are encrypted inside `group_vars/all/vault.yml`. To update your AWS access credentials securely, run:
```bash
ansible-vault edit group_vars/all/pass.yml
```

---

## 💻 Usage

### 1. Spin Up the Infrastructure
To provision the 3 EC2 instances and execute their initial configuration routines, run:
```bash
ansible-playbook ec2_create.yaml --vault-password-file vault.pass
```

### 2. Stop the Ubuntu Instances
To safely stop only the Ubuntu instances when they are not in use (optimizing resource consumption), run the dedicated management playbook:
```bash
ansible-playbook ec2_stop.yaml --vault-password-file vault.pass


