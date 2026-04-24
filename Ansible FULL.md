# Ansible Automation Course: Comprehensive Outline

## 1. Course Introduction
Ansible is an open-source IT automation engine that simplifies provisioning, configuration management, and application deployment. It is designed to be multi-tier, ensuring that all parts of an IT infrastructure work together.
*   **Agentless Architecture**: Unlike other tools, Ansible doesn't require "agent" software on the managed nodes; it uses standard SSH (Linux) or WinRM (Windows).
*   **YAML-Based**: Uses human-readable YAML for automation "playbooks," making it accessible to non-programmers.

## 2. The Automation Advantage
Moving from manual scripts to Ansible provides enterprise-level benefits:
*   **Efficiency**: Reduces the time spent on repetitive tasks.
*   **Consistency**: Eliminates "configuration drift" by ensuring every server is configured identically.
*   **Scalability**: Easily manage hundreds or thousands of servers with the same effort used for one.

## 3. Ansible Basics
The core building blocks of any Ansible project:
*   **Control Node**: The machine where Ansible is installed and commands are run.
*   **Managed Nodes**: The target servers being automated.
*   **Inventory**: A file (usually INI or YAML) that lists the managed nodes and their groups.
*   **Modules**: The discrete "units of work" (e.g., `apt`, `copy`, `service`) that perform specific actions.

## 4. Ansible Variables
Variables allow you to make playbooks dynamic and reusable across different environments.
*   **Scope**: Can be defined at the global, play, or host level.
*   **Precedence**: Ansible has a specific hierarchy for variable overrides (e.g., command-line "extra vars" have the highest priority).
*   **Facts**: Automatically gathered system information (IP, OS version, disk space) used as variables during execution.

## 5. Ansible Constructs
Constructs control the flow and logic of your automation:
*   **Conditionals (`when`)**: Run tasks only if certain criteria are met (e.g., "only install if OS is Ubuntu").
*   **Loops (`loop`, `with_items`)**: Repeat a task multiple times, such as installing a list of packages or creating several users.
*   **Handlers**: Special tasks that only run when "notified" by another task (commonly used for restarting services after a config change).

## 6. Ansible Templates
Templates use the **Jinja2** engine to create dynamic configuration files.
*   **Dynamic Content**: Instead of copying a static file, templates use variables to customize files for each specific host (e.g., setting a unique `server_id` in a database config).
*   **Logic in Files**: You can use `if` statements and `for` loops directly inside the configuration file template.

## 7. Ansible Roles
Roles are a way to bundle automation content (tasks, variables, files, and templates) into a reusable, modular structure.
*   **Standardized Directory**: Follows a specific folder structure (`tasks/`, `vars/`, `defaults/`, `templates/`).
*   **Reusability**: Roles allow you to share your automation logic with others or reuse it across different playbooks easily.

## 8. Ansible Collections
Collections are the current standard for distributing Ansible content.
*   **Package Deal**: They bundle modules, plugins, roles, and playbooks together.
*   **Namespace**: Organized by "namespace.collection_name" (e.g., `community.general` or `cisco.ios`) to prevent naming conflicts.

## 9. Role-Based Access Control (RBAC)
Part of the Ansible Automation Platform (via Controller/Tower), RBAC ensures security.
*   **Permissions**: Defines who can run specific playbooks or access certain inventories.
*   **Teams and Orgs**: Groups users into teams and organizations to manage access at scale within a company.

## 10. Ansible Workflows
Workflows allow you to link multiple playbooks together into a single pipeline.
*   **Visual Logic**: You can set conditions like "if Playbook A succeeds, run Playbook B; if it fails, run Playbook C."
*   **Complex Orchestration**: Ideal for multi-step processes like provisioning a VM, then configuring it, then adding it to a load balancer.

## 11. Event-Driven Ansible (EDA) Overview
The "Next Gen" of automation that responds to real-world events.
*   **Rulebooks**: Uses "if-this-then-that" logic.
*   **Automated Remediation**: For example, if a monitoring tool detects a service is down, EDA can automatically trigger a playbook to restart it or investigate the logs without human intervention.


# Module: An Introduction to Ansible

## 1. Fundamental Concepts
Ansible is an automation engine that simplifies complex IT tasks. It operates on a few core principles:
*   **Agentless Architecture**: It uses standard communication protocols (SSH for Linux/Unix, WinRM or SSH for Windows) to execute tasks, meaning you don't need to install "agent" software on your target servers.
*   **Idempotency**: A key feature where a task is only executed if the system is not already in the desired state. If the target is already configured correctly, Ansible skips the task, ensuring safety and predictability.
*   **Declarative Language**: Instead of writing scripts that list every step to take, you describe the **desired end state** of the system using YAML.
*   **Push-Based**: Commands are usually initiated from a central "Control Node" and pushed out to the "Managed Nodes."

## 2. Setting Up VS Code for Ansible Development
To create a professional development environment, follow these steps:
1.  **Install VS Code**: Ensure the latest version of Visual Studio Code is installed.
2.  **Ansible Extension**: Install the official **Ansible extension by Red Hat**. This provides syntax highlighting, code completion (intellisense), and integration with `ansible-lint`.
3.  **YAML Extension**: Install the **YAML extension by Red Hat** to help with formatting and structural validation.
4.  **Python Setup**: Since Ansible is Python-based, ensure a Python interpreter is selected in VS Code to enable better linting and path resolution.

## 3. Configuring Ansible Settings
Ansible’s behavior is controlled via the `ansible.cfg` file. It looks for this file in the following order: `ANSIBLE_CONFIG` (env variable), `ansible.cfg` (current directory), `~/.ansible.cfg` (home directory), and finally `/etc/ansible/ansible.cfg`.

Key settings to configure in your local `ansible.cfg`:
*   **`inventory`**: Points to your default host file (e.g., `./inventory`).
*   **`remote_user`**: Specifies the default username to use when connecting to managed nodes.
*   **`host_key_checking`**: Set to `False` in lab environments to avoid manual SSH fingerprint confirmation (use with caution in production).
*   **`forks`**: Determines how many hosts Ansible communicates with simultaneously (default is 5).

### Example `ansible.cfg`
```ini
[defaults]
inventory = ./inventory
remote_user = admin
host_key_checking = False
forks = 10

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```


# Module: Developing Automation Content

## 1. Building Ansible Inventories
An inventory defines the "where" of your automation. It can be a simple static file or a dynamic script that pulls data from a cloud provider.

*   **Static Inventory (INI or YAML)**: Lists hostnames or IP addresses, often grouped by function (e.g., `[webservers]`, `[dbservers]`).
*   **Inventory Variables**: You can define variables specific to a host or a group directly within the inventory to customize behavior.
*   **Groups of Groups**: Use the `:children` suffix to nest groups (e.g., a `[production]` group containing both `[web]` and `[db]`).

**Example (INI format):**
```ini
[web]
://example.com
://example.com

[db]
://example.com

[datacenter:children]
web
db
```

## 2. Writing and Running Playbooks
Playbooks are the "instruction manuals" written in YAML.

*   **Structure**: A playbook consists of one or more **plays**. Each play maps a group of hosts to a series of **tasks**.
*   **Simple Playbooks**: Focus on a single goal, such as ensuring a specific package is installed.
*   **Complex Playbooks**: Utilize variables, loops, conditionals, and multiple plays to coordinate multi-tier deployments (e.g., updating a database before restarting web servers).

**Running a Playbook:**
Use the `ansible-playbook` command:
```bash
ansible-playbook -i inventory site.yml
```

## 3. Troubleshooting Playbooks and Host Failures
When automation fails, Ansible provides several tools to identify and fix the issue.

*   **Verbose Mode**: Add `-v`, `-vv`, or `-vvv` to your command to see increasingly detailed output, including connection attempts and module return data.
*   **Syntax Check**: Use `--syntax-check` to verify the YAML structure without actually running any tasks.
*   **Check Mode (`--check`)**: A "dry run" that predicts what changes would occur without actually modifying the managed nodes.
*   **Ansible Lint**: A command-line tool (`ansible-lint`) that checks playbooks for best practices and common pitfalls.
*   **Handling Host Failures**: 
    *   **`ignore_errors: yes`**: Allows the playbook to continue even if a specific task fails.
    *   **`failed_when`**: Customizes what constitutes a "failure" based on specific output strings or return codes.
    *   **`any_errors_fatal`**: Stops the entire play across all hosts if even a single host fails.

**Example: Debugging Task**
```yaml
- name: Check variable value
  debug:
    msg: "The value of my_var is {{ my_var }}"
```


# Module: Developing Automation Content: Variables

## 1. Using Variables to Simplify Management
Variables allow you to write generic playbooks that can be reused across different environments (Dev, Test, Prod) without changing the code itself.
*   **Variable Definition**: Variables can be defined in `vars` blocks within playbooks, in `group_vars/` or `host_vars/` directories, or passed via the command line with `-e`.
*   **Dynamic Content**: Use the `{{ variable_name }}` syntax to reference values.
*   **Separation of Concerns**: By keeping configuration values separate from the task logic, you reduce the risk of accidental errors when updating settings.

## 2. Protecting Sensitive Data
To secure passwords, API keys, and other secrets, Ansible provides **Ansible Vault**.
*   **Encryption**: Use `ansible-vault encrypt <file>` to scramble sensitive variable files.
*   **Decryption at Runtime**: When running a playbook, use the `--ask-vault-pass` flag or point to a password file with `--vault-password-file`.
*   **In-line Secrets**: You can encrypt a single string instead of a whole file using `ansible-vault encrypt_string`, allowing you to embed secrets directly into a YAML file.

## 3. Facts and Magic Variables
Ansible automatically discovers details about managed hosts and provides internal variables for advanced logic.
*   **Ansible Facts**: Data gathered during the "setup" phase (e.g., `ansible_distribution`, `ansible_memtotal_mb`, `ansible_interfaces`). You can disable this with `gather_facts: no` to speed up execution.
*   **Magic Variables**: Special variables provided by Ansible that are not gathered from the host:
    *   `hostvars`: Access variables belonging to other hosts in the inventory.
    *   `groups`: A list of all hosts in the inventory, organized by group name.
    *   `inventory_hostname`: The name of the current host as configured in the inventory.

### Example: Using Facts and Vault
```yaml
- name: Configure Web Server
  hosts: webservers
  vars_files:
    - secrets.yml  # This file is encrypted with Ansible Vault
  tasks:
    - name: Ensure Apache is installed on Linux
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"  # Using a Fact

    - name: Set DB password from Vault
      template:
        src: db_config.j2
        dest: /etc/db.conf
```


# Module: Developing Automation Content: Task Control

## 1. Loops: Iterating Over Lists
Loops allow you to execute a single task multiple times with different values, reducing code duplication and making playbooks easier to maintain.

*   **`loop` Keyword**: The standard way to iterate over a simple list of items.
*   **Accessing Data**: Each item in the list is referenced using the `{{ item }}` variable.
*   **Complex Lists**: You can loop over dictionaries (key-value pairs) to handle more detailed data, like creating users with specific shell and group settings.

**Example: Installing Multiple Packages**
```yaml
- name: Install required tools
  ansible.builtin.package:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - vim
    - curl
```

## 2. Conditionals: Execution Logic
Conditionals ensure that tasks only run when specific criteria are met, allowing your playbook to adapt to different environments or host states.

*   **`when` Statement**: Evaluates a Boolean expression before running the task.
*   **Using Facts**: Frequently used to target specific operating systems (e.g., `ansible_os_family == "RedHat"`).
*   **Registering Variables**: You can capture the output of one task using `register` and use its result in a conditional for a subsequent task (e.g., "Run this task only if the previous command failed").

**Example: Conditional Service Restart**
```yaml
- name: Restart service if config exists
  ansible.builtin.service:
    name: httpd
    state: restarted
  when: config_check.stat.exists
```

## 3. Handlers: Reactive Task Control
Handlers are a specialized form of task control used for actions that should only occur when a change is detected.

*   **`notify`**: A task can "notify" a handler if it results in a "changed" status.
*   **Execution Order**: Handlers run once at the very end of the play, regardless of how many tasks notified them. This prevents redundant actions, such as restarting a service five times for five different configuration changes.

**Example: Notifying a Handler**
```yaml
- name: Update Apache config
  ansible.builtin.template:
    src: httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
  notify: Restart Apache

handlers:
  - name: Restart Apache
    ansible.builtin.service:
      name: httpd
      state: restarted
```


# Module: Developing Automation Content: Deploying Files

## 1. Deploying Static Files
The `copy` module is the primary tool for moving files from the control node to managed hosts.
*   **Simple Transfers**: Use it to send fixed assets like scripts, static web pages, or certificates.
*   **Permissions and Ownership**: You can explicitly set the owner, group, and mode (e.g., `0644`) during the transfer.
*   **Backup**: Setting `backup: yes` ensures that if a file already exists on the host, a timestamped copy is created before it is overwritten.

## 2. Customizing Files with Templates
When files need to be unique to each host, Ansible uses the **Jinja2** templating engine.
*   **The `template` Module**: Similar to `copy`, but it processes the file on the control node first, replacing placeholders with actual variable values.
*   **Dynamic Logic**: You can use Python-like syntax inside templates (e.g., `{% if ... %}` or `{% for ... %}`) to include or exclude lines based on the host's facts or inventory variables.
*   **Extension**: Templates typically end in `.j2` to distinguish them from static files.

## 3. Adjusting Existing Files
If you only need to modify a specific line or section of an existing file rather than replacing the whole thing, Ansible provides specialized modules:
*   **`lineinfile`**: Ensures a specific line is present or absent in a file. Great for simple tweaks like changing a single setting in a config file.
*   **`replace`**: Uses regular expressions to find and replace all instances of a pattern within a file.
*   **`blockinfile`**: Inserts or updates a multi-line block of text, usually surrounded by marker lines (e.g., `# BEGIN ANSIBLE MANAGED BLOCK`) so Ansible can manage that specific section without touching the rest of the file.

### Example: Template vs. Line-in-File
```yaml
# Using a Template for a full config
- name: Deploy dynamic Nginx config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf

# Using lineinfile for a small change
- name: Enable password authentication in SSH
  ansible.builtin.lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PasswordAuthentication'
    line: 'PasswordAuthentication yes'
```

# Module: Developing Automation Content at Scale

## 1. Organizing Content: Imports vs. Includes
As playbooks grow, keeping everything in a single file becomes unmanageable. Ansible allows you to break content into smaller, reusable files using two primary methods:

*   **Imports (`import_*`)**: 
    *   **Static**: These are processed at the moment the playbook is parsed (before execution begins).
    *   **Behavior**: You cannot use variables from tasks within the same play to determine which file to import.
    *   **Use Case**: Ideal for large, fixed structures like importing a common set of "base" tasks for all servers.
*   **Includes (`include_*`)**: 
    *   **Dynamic**: These are processed "on the fly" as the playbook hits that specific task during execution.
    *   **Behavior**: You can use loops or conditionals to decide if/when a task file should be included.
    *   **Use Case**: Ideal for complex logic, such as including a specific set of tasks only if a previous task succeeded.

## 2. Managing Multiple Playbooks
You can create a "Master Playbook" that orchestrates several smaller playbooks.
*   **`import_playbook`**: Allows you to sequence multiple playbooks. For example, a `site.yml` might import a `web_stack.yml`, a `db_stack.yml`, and a `monitoring.yml` to deploy an entire infrastructure in one command.

## 3. Advanced Host Patterns
When running automation at scale, you often need to target a precise subset of your inventory without creating new permanent groups.

*   **Logical Operators**:
    *   **Intersection (`&`)**: Target hosts that exist in both groups. (e.g., `webservers:&production`)
    *   **Exclusion (`!`)**: Target hosts in one group but not another. (e.g., `webservers:!staging`)
*   **Wildcards and Regex**: Use `*` to match patterns (e.g., `*.example.com`) or `~` to start a regular expression.
*   **List Position**: Access specific hosts by their index in a group (e.g., `webservers[0]` targets the first host, `webservers[0:5]` targets the first six).

### Example: Scaling Logic
```yaml
# Master Playbook (site.yml)
- name: Orchestrate Data Center Deployment
  import_playbook: infrastructure/network_setup.yml

- name: Configure Production Web Servers (excluding the first one)
  import_playbook: app/web_deploy.yml
  hosts: "webservers:&production:!webservers[0]"
```

# Module: Reusing Code with Ansible Roles and Content Collections

## 1. Modularizing with Ansible Roles
Roles provide a framework for fully independent and reusable units of automation. They allow you to bundle tasks, variables, files, templates, and handlers into a standardized file structure.

*   **Standard Directory Structure**: A role is organized into specific folders:
    *   `tasks/main.yml`: The primary list of tasks the role executes.
    *   `handlers/main.yml`: Handlers used by this role.
    *   `defaults/main.yml`: Default variables (lowest priority, meant to be easily overridden).
    *   `vars/main.yml`: Higher priority variables specific to the role.
    *   `templates/` and `files/`: Assets the role deploys.
*   **Benefits**: Roles make playbooks cleaner and allow you to share automation across different projects or teams without rewriting code.

## 2. Speeding Up Development with Ansible Content Collections
Collections are the modern distribution format for Ansible content, including modules, plugins, and roles.

*   **Pre-built Solutions**: Instead of writing automation from scratch, you can use collections maintained by vendors (like AWS, Cisco, or VMware) or the community.
*   **Fully Qualified Collection Name (FQCN)**: To ensure you are using the correct module, you reference it using its full name (e.g., `community.general.htpasswd` or `amazon.aws.ec2_instance`).
*   **Ansible Galaxy**: The public hub for finding and downloading roles and collections. You can install them using the command:
    ```bash
    ansible-galaxy collection install namespace.name
    ```

## 3. Implementing Roles and Collections in Playbooks
You can call roles directly within a play or use the `collections` keyword to simplify module referencing.

**Example: Playbook using a Role and a Collection**
```yaml
- name: Deploy Web Application
  hosts: webservers
  collections:
    - community.general  # Allows using modules from this collection without FQCN
  roles:
    - common_baseline    # Custom local role for security hardening
    - geerlingguy.nginx  # Community role for Nginx setup
  tasks:
    - name: Use a module from the collection
      slack:
        token: "{{ slack_token }}"
        msg: "Deployment on {{ inventory_hostname }} is starting!"
```


# Module: Automating Linux Administration Tasks

## 1. Managing Users and Security
Ansible simplifies identity and access management across hundreds of Linux nodes simultaneously.
*   **User Management**: Use the `user` module to create or remove users, set UIDs, and manage group memberships.
*   **SSH Key Distribution**: Use the `authorized_key` module to push public keys to specific users, enabling secure, passwordless access.
*   **Sudoers Configuration**: Automate administrative privileges by deploying snippets to `/etc/sudoers.d/` using the `copy` or `template` modules.

## 2. Software and Package Management
Maintaining consistent software versions is a core administrative task.
*   **Package States**: Use the `package` module (a generic wrapper) or OS-specific modules like `dnf`, `apt`, or `zypper` to ensure software is `present`, `absent`, or at the `latest` version.
*   **Repository Management**: Automate the addition of external repositories (e.g., Docker, EPEL) using `apt_repository` or `yum_repository`.

## 3. Storage and File Systems
Ansible can manage the entire lifecycle of storage, from partitioning to mounting.
*   **Partitioning**: Use the `parted` module to manage disk partitions.
*   **Logical Volume Management (LVM)**: Use `lvg` (Volume Groups) and `lvol` (Logical Volumes) to manage flexible storage pools.
*   **File Systems**: The `filesystem` module formats partitions (ext4, xfs, etc.), while the `mount` module manages the `/etc/fstab` entries and mounts the devices.

## 4. System Services and Maintenance
*   **Service Control**: Use the `service` or `systemd` modules to ensure critical daemons (like `sshd`, `crond`, or `nginx`) are started, enabled on boot, or reloaded.
*   **Cron Jobs**: Use the `cron` module to schedule recurring tasks (backups, log rotation) without manually editing crontab files.
*   **System Boot/Reboot**: The `reboot` module allows Ansible to restart a machine and wait for it to come back online before continuing the playbook.

### Example: Basic System Hardening Task
```yaml
- name: Standard Linux Admin Tasks
  hosts: all
  tasks:
    - name: Ensure 'sysadmin' group exists
      ansible.builtin.group:
        name: sysadmin
        state: present

    - name: Create a deploy user with sudo access
      ansible.builtin.user:
        name: deploy
        groups: sysadmin
        append: yes

    - name: Update all packages to latest version
      ansible.builtin.package:
        name: "*"
        state: latest

    - name: Ensure NTP service is running and enabled
      ansible.builtin.service:
        name: chronyd
        state: started
        enabled: yes
```


