## Ansible Ad-hoc commands
An Ansible ad hoc command uses the /usr/bin/ansible command-line tool to automate a single task on one or more managed nodes. ad hoc commands are quick and easy, but they are not reusable.

- you could execute a quick one-liner in Ansible without writing a playbook. An ad hoc command looks like this:

- $ ansible [pattern] -m [module] -a "[module options]"

The `-a` option accepts options either through the `key=value` syntax or a JSON string starting with `{` and ending with `}` for more complex option structure. You can learn more about [patterns](https://docs.ansible.com/ansible/latest/inventory_guide/intro_patterns.html#intro-patterns) and [modules](https://docs.ansible.com/ansible/latest/plugins/module.html#module-plugins) on other pages.

---

## **Ansible ad-hoc commands have a simple syntax that you can use to perform quick tasks across your infrastructure**
- `ansible <host-pattern> -i <inventory-file> -m <module-name> -a "<module-arguments>" [options]`

### **Explanation of Parameters:**

- **`ansible`**: The Ansible command-line tool.
- **`<host-pattern>`**: Specifies the target hosts (e.g., `all`, `web`, `db`, or an individual host).
- **`-i <inventory-file>`**: Specifies the inventory file that contains the hosts you want to target.
- **`-m <module-name>`**: Specifies the Ansible module you want to use (e.g., `apt`, `yum`, `file`, `ping`, `copy`, etc.).
- **`-a "<module-arguments>"`**: Arguments passed to the module (e.g., the package name, file path, or other options).
- **`[options]`**: Optional flags like `--become` for privilege escalation, `--check` for dry-run, etc.


### **Common Options for Ad-Hoc Commands:**

- **`--become`**: Elevates privileges to execute tasks as a superuser (==useful for package installations or service management==).
- **`--check`**: Performs a ==dry-run== of the playbook or command, showing what changes would be made without actually applying them.
- **`--diff`**: Shows a diff of what changes will be made (e.g., when modifying files).
- **`--ask-become-pass`**: Prompts for the sudo password (if required) when using `--become`

### **Host Patterns:**

- **`all`**: Targets all hosts in the inventory.
- **`web`**: Targets the `web` group in the inventory.
- **`db[0:5]`**: Targets a range of hosts, like `db0`, `db1`, `db2`, etc.
- **`localhost`**: Targets the local machine (useful for testing or local actions).
- **`<hostname>`**: Targets a specific host by name or IP address.

---
## **Important Ad-hoc commands**
#### **1. Basic Commands for System Management**
1) Ping all hosts
    - `ansible all -i inventory.ini -m ping`
     * * for particular host
     `ansible <hostname> -i inventory.ini -m ping`

2) **Gather Facts (System Information):**
     - `ansible all -i inventory.ini -m setup`
     - for specific tasks use filter 
       `ansible all -i inventory.ini -m setup -a "filter=<fact_name>`
   eg .
    1) **Gather only memory-related facts:**
        `ansible all -i inventory.ini -m setup -a "filter=ansible_memory_mb"`
    2) Gather only network-related facts: 
        `ansible all -i inventory.ini -m setup -a "filter=ansible_default_ipv4,ansible_all_interfaces`
    3)  Gather only disk-related facts:
        ``ansible all -i inventory.ini -m setup -a "filter=ansible_devices

3) Check System Uptime
    - ``ansible all -i inventory.ini -m command -a "uptime

---
#### **2.Package Management (Install/Remove Software)**

1) Install Package 
    - `ansible all -i inventory.ini -m apt -a "name=nginx state=present" --become`  (debian based eg. ubuntu) change to apt for Redhat based
      - became uses root permissions

2) Remove a package
     - `ansible all -i inventory.ini -m apt -a "name=nginx state=absent" --become`

3) Upgrade All Packages- Upgrades all packages on all hosts.
     - `ansible all -i inventory.ini -m apt -a "upgrade=dist" --become`

---
#### **3.**File and Directory Management

1) Create a Directory
     - `ansible all -i inventory.ini -m file -a "path=/tmp/testdir state=directory mode=0755`
     - Creates a directory at `testdir`


2) Copy a File to Remote Hosts
     - `ansible all -i inventory.ini -m copy -a "src=/local/file dest=/remote/file mode=0644`

3) Delete a File or Directory
     - `ansible all -i inventory.ini -m file -a "path=/tmp/testfile state=absent`

4)  Change File Permissions
     - `ansible all -i inventory.ini -m file -a "path=/tmp/testfile mode=0755`


---

#### **4. Service Management**

1) Start a service
     - `ansible all -i inventory.ini -m service -a "name=nginx state=started" --become`

2) Stop a Service:
     - `ansible all -i inventory.ini -m service -a "name=nginx state=stopped" --become`

3) Restart a service:
     - `ansible all -i inventory.ini -m service -a "name=nginx state=restarted" --become`

4) Enable a service from boot: 
      - `ansible all -i inventory.ini -m service -a "name=nginx enabled=yes" --become`    
      - `enabled=no` - Disable a Service from boot

---
#### **5. User and Group Management**

1) Create a User:
     - `ansible all -i inventory.ini -m user -a "name=deploy state=present" --become`
     - Creates the `deploy` user on all hosts.

2)  Delete a User:
     - `ansible all -i inventory.ini -m user -a "name=deploy state=absent" --become`

3) Add user to group:
     - `ansible all -i inventory.ini -m user -a "name=deploy groups=sudo append=yes" --become`
     - Adds the `deploy` user to the `sudo` group on all hosts

---

#### **6. Network Management**

1) Check if a Port is Open:

     - `ansible all -i inventory.ini -m shell -a "netstat -tuln | grep :80"`

     - Checks if port `80` is open on all remote hosts.

2) Download a File from the Internet:

     - `ansible all -i inventory.ini -m get_url -a "url=https://example.com/file.tar.gz dest=/tmp/file.tar.gz"`

     -  Downloads a file from a URL to the `/tmp` directory on all hosts.

---

#### **7.Process and System Management**
1) Reboot Host 
     - `ansible all -i inentory.ini -m reboot --become`

2) Kill a process
     - `ansible all -i inventory.ini -m shell -a "pkill -f provess_name" --become`

3) Check a disk space
     - `ansible web -i inventory.ini -m shell -a "df -h"`
     - uses only web tagged machines in inventory file

4) Show running processes: 
     - `ansible all -i inventory.ini -m shell -a "ps aux"`

---

#### **8.Docker Management**

1) Start Docker Container:
     - `ansible all -i inventory.ini -m docker_container -a "name=my_cont image=nginx state=started"`

2) Stop a docker container 
     - `ansible all -i inventory.ini -m docker_container -a "name=my_cont state=stopped"`
--- 
#### **9. Log Management**

1) Tail log file
     - `ansible all -i inventory.ini -m shell -a "tail -n 10 /var/log/nginx/access.log" --become`

---

#### **10.Other**

1) Execute a Shell Command:
     - `ansible all -i inventory.ini -m shell -a "echo Hello, World!"`
     - Executes a simple shell command (`echo Hello, World!`) on all hosts

2) Create file with content 
     - `ansible all -i inventory.ini -m copy -a "content='Hello, World!' dest=/usr/share/nginx/html/hello.html"`