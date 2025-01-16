## Overview
This Ansible playbook is designed to harden SUSE Linux systems based on the CIS Benchmarks, ensuring your system is secure and compliant. It incorporates both Level 1 and Level 2 security recommendations, with a focus on configurations that do not require installing additional software on the target system.

---

## Requirements

### Target Machine
1. **OS**: SUSE Linux Enterprise or compatible distribution.
2. **Python Version**: Python 3.11 installed and set as the default `python3` interpreter.
3. **Required Directories**: The following directories must exist:
   - `/etc/modprobe.d`
   - `/etc/sysctl.d`
   - `/etc/security`
4. **Firewall**: If firewalld is required, it must already be installed and available.

### Control Machine
1. **Ansible Version**: Ansible 2.14 or newer.
2. **SSH Access**: Ensure passwordless SSH access to the target machine.
3. **Inventory Configuration**: Configure the target machine in the Ansible inventory file with the correct user and Python interpreter path:
   ```ini
   [suse_vm]
   10.12.50.29 ansible_user=admin ansible_ssh_private_key_file=/path/to/private/key ansible_python_interpreter=/usr/bin/python3.11
   ```

---

## Playbook Structure

### Variables
- `cis_level`: Define the hardening level (\"1\" for Level 1, \"2\" for Level 2). Defaults to \"2\".
- `audit_rules_file`: Path to the audit rules configuration file (`/etc/audit/rules.d/cis.rules`).
- `level_1_critical_services`: List of services to disable for Level 1 compliance.
- `level_2_critical_services`: List of services to disable for Level 2 compliance.

### Key Sections
1. **Initial Setup**: Ensures necessary directories and partitions are configured.
2. **Filesystem Configuration**: Applies module restrictions.
3. **Logging and Auditing**: Configures auditd and deploys audit rules.
4. **Kernel Parameters**: Configures system parameters using sysctl.
5. **Authentication and Password Policies**: Enforces password complexity and expiration policies.
6. **Service Hardening**: Disables unnecessary services.
7. **Firewall Configuration**: Configures firewalld if available.

---

## Usage

### Step 1: Set Up Inventory File
Create an inventory file (e.g., `inventory.ini`) with the target machine details:
```ini
[suse_vm]
10.12.50.29 ansible_user=admin ansible_ssh_private_key_file=/path/to/private/key ansible_python_interpreter=/usr/bin/python3.11
```

### Step 2: Execute the Playbook
Run the playbook using the following command:
```bash
ansible-playbook -i inventory.ini suse_hardening.yml
```

### Step 3: Verify Changes
After the playbook completes, verify that the hardening measures have been applied:
1. Check `/etc/fstab` for partition configurations.
2. Confirm audit rules in `/etc/audit/rules.d/cis.rules`.
3. Test sysctl parameters:
   ```bash
   sysctl -a | grep <parameter>
   ```
4. Ensure unnecessary services are disabled:
   ```bash
   systemctl status <service_name>
   ```

---

## Troubleshooting

### Common Issues
1. **Python Version Errors**:
   - Ensure Python 3.11 is installed and set as the default `python3` on the target machine.
2. **Permissions Issues**:
   - Ensure the Ansible user has sudo privileges on the target machine.
3. **Missing Dependencies**:
   - Verify that all required directories exist before running the playbook.

### Debugging
Run the playbook in verbose mode to troubleshoot:
```bash
ansible-playbook -i inventory.ini suse_hardening.yml -vvv
```

---

## Additional Notes
- This playbook is designed for systems that already have required software (e.g., firewalld, auditd) installed.
- For additional customization, edit the variable definitions in the playbook.
- Test the playbook in a staging environment before deploying to production.

---

## License
This playbook is open-source and provided \"as is\" without warranty of any kind.
