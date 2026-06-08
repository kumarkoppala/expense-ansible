# expense-ansible
```
my-ansible-project/
├── inventory.ini             # Your list of frontend/backend servers
├── playbook.yml              # Your main execution file
├── group_vars/               # 👈 ROOT LEVEL (Not inside roles)
│   ├── all.yml               # 🌐 Applies to ALL servers and ALL roles
│   ├── frontend.yml          # 💻 Applies ONLY to frontend hosts
│   └── backend.yml           # ⚙️ Applies ONLY to backend hosts
└── roles/
    ├── web_server/           # Role for frontend services
    │   ├── tasks/main.yml
    │   └── defaults/main.yml # 👈 Role defaults go here, NOT group_vars
    └── db_server/            # Role for database services
        ├── tasks/main.yml
        └── defaults/main.yml
```
# Ansible Modules Quick Revision Guide (Linux)

## 📁 File & Directory Management
* **file:** Creates, deletes, and modifies permissions of files and directories.
  ```yaml
  - name: Create a directory with permissions
    ansible.builtin.file:
      path: /etc/config
      state: directory
      mode: '0755'
  ```
* **copy:** Copies local files from the control node to remote servers.
  ```yaml
  - name: Copy a local file to remote server
    ansible.builtin.copy:
      src: /local/path/file.txt
      dest: /remote/path/file.txt
      mode: '0644'
  ```
* **template:** Deploys dynamic configuration files using Jinja2 variables.
  ```yaml
  - name: Deploy dynamic configuration template
    ansible.builtin.template:
      src: config.j2
      dest: /etc/myapp/config.conf
  ```
* **fetch:** Copies files from remote target servers back to the local machine.
  ```yaml
  - name: Fetch logs from remote server
    ansible.builtin.fetch:
      src: /var/log/syslog
      dest: /local/logs/
      flat: yes
  ```
* **lineinfile:** Finds, updates, or deletes a specific single line inside a text file.
  ```yaml
  - name: Ensure port 8080 is set in config
    ansible.builtin.lineinfile:
      path: /etc/httpd/conf/httpd.conf
      regexp: '^Listen '
      line: Listen 8080
  ```
* **blockinfile:** Inserts, updates, or removes multi-line blocks of text with markers.
  ```yaml
  - name: Add multiple configuration lines
    ansible.builtin.blockinfile:
      path: /etc/hosts
      block: |
        192.168.1.50 host1.local
        192.168.1.51 host2.local
  ```
* **replace:** Finds and replaces all matching text patterns using regular expressions (regex).
  ```yaml
  - name: Replace old domain name with new one
    ansible.builtin.replace:
      path: /etc/nginx/nginx.conf
      regexp: 'olddomain\.com'
      replace: 'newdomain.com'
  ```
* **stat:** Retrieves detailed information and facts about a file or directory.
  ```yaml
  - name: Check if file exists
    ansible.builtin.stat:
      path: /etc/passwd
    register: file_info
  ```

## 🛠️ System & Administration
* **service:** Controls system services by stopping, starting, restarting, or reloading them.
  ```yaml
  - name: Restart Apache service and enable it
    ansible.builtin.service:
      name: httpd
      state: restarted
      enabled: yes
  ```
* **user:** Creates, updates, locks, or deletes system user accounts.
  ```yaml
  - name: Create a new user with a bash shell
    ansible.builtin.user:
      name: johndoe
      shell: /bin/bash
      groups: wheel,admin
  ```
* **group:** Manages system user groups and memberships.
  ```yaml
  - name: Ensure developers group exists
    ansible.builtin.group:
      name: developers
      state: present
  ```
* **cron:** Schedules and manages automated background tasks using the system crontab.
  ```yaml
  - name: Run backup script every midnight
    ansible.builtin.cron:
      name: "Daily Backup"
      hour: "0"
      minute: "0"
      job: "/usr/local/bin/backup.sh"
  ```
* **hostname:** Sets, updates, or manages the target system hostname.
  ```yaml
  - name: Change host name
    ansible.builtin.hostname:
      name: webserver-prod
  ```
* **reboot:** Restarts a managed machine and safely waits for it to come back online.
  ```yaml
  - name: Reboot machine after updates
    ansible.builtin.reboot:
      reboot_timeout: 300
  ```

## 📦 Package & Source Control
* **package:** Installs or removes packages using the OS-native package manager automatically.
  ```yaml
  - name: Install git on any OS
    ansible.builtin.package:
      name: git
      state: present
  ```
* **apt:** Manages software packages specifically on Debian and Ubuntu systems.
  ```yaml
  - name: Install nginx on Ubuntu/Debian
    ansible.builtin.apt:
      name: nginx
      state: present
      update_cache: yes
  ```
* **yum:** Manages software packages specifically on RHEL and CentOS 7 systems.
  ```yaml
  - name: Install curl on CentOS 7
    ansible.builtin.yum:
      name: curl
      state: present
  ```
* **dnf:** Manages software packages specifically on newer RHEL and Fedora 8+ systems.
  ```yaml
  - name: Install mariadb on Fedora
    ansible.builtin.dnf:
      name: mariadb-server
      state: present
  ```
* **git:** Clones, updates, or manages code repositories directly from Git source control.
  ```yaml
  - name: Clone a git repository
    ansible.builtin.git:
      repo: 'https://github.com'
      dest: /var/www/html
      version: main
  ```

## 💻 Execution & Fallbacks
* **command:** Executes raw commands on remote nodes without processing through a shell environment.
  ```yaml
  - name: Run a basic command
    ansible.builtin.command: /usr/bin/uptime
  ```
* **shell:** Executes commands through the remote node shell, allowing pipes and environment variables.
  ```yaml
  - name: Run a shell command with pipe
    ansible.builtin.shell: cat /var/log/nginx/access.log | grep "404"
  ```
* **script:** Runs a local script on remote nodes after automatically transferring it.
  ```yaml
  - name: Transfer and execute local script on targets
    ansible.builtin.script: /local/scripts/setup_env.sh
  ```

## 🛡️ Networking & Security
* **firewalld:** Manages firewall zones, services, and rules on RHEL-based systems.
  ```yaml
  - name: Permit HTTPS public traffic
    ansible.posix.firewalld:
      service: https
      permanent: yes
      state: enabled
  ```
* **ufw:** Manages firewall rules and policies on Ubuntu and Debian systems.
  ```yaml
  - name: Allow SSH connection through UFW
    community.general.ufw:
      rule: allow
      name: OpenSSH
  ```
* **get_url:** Downloads files securely from HTTP, HTTPS, or FTP servers to targets.
  ```yaml
  - name: Download tool binary
    ansible.builtin.get_url:
      url: https://example.com
      dest: /tmp/tool.tar.gz
  ```

## 🔍 Diagnostics & Facts
* **debug:** Prints custom messages and variable values to the console during execution.
  ```yaml
  - name: Show the registered file information
    ansible.builtin.debug:
      msg: "File exists status: {{ file_info.stat.exists }}"
  ```
* **setup:** Automatically gathers extensive system facts and hardware data from remote hosts.
  ```yaml
  - name: Manually gather server facts
    ansible.builtin.setup:
  ```

