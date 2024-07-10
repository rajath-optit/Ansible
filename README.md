## **Guide to Install and Set Up Ansible on Ubuntu**

### 1. **Update Your System**

First, ensure your system packages are up-to-date:

```bash
sudo apt update
sudo apt upgrade
```

### 2. **Install Ansible**

You can install Ansible from the official Ubuntu repository:

```bash
sudo apt install ansible
```

To verify that Ansible is installed correctly, run:

```bash
ansible --version
```

You should see output indicating the Ansible version.

### 3. **Basic Configuration**

Ansible’s main configuration file is located at `/etc/ansible/ansible.cfg`. You can modify this file to set global configurations.

To edit the configuration file:

```bash
sudo nano /etc/ansible/ansible.cfg
```

**Common Configuration Settings:**

```ini
[defaults]
# Set the path to your inventory file
inventory = /etc/ansible/hosts

# Define the default SSH user for remote connections
remote_user = your_username

# Specify the default module path
library = /usr/share/ansible/plugins/modules

# Control SSH connection settings
host_key_checking = False

# Set the default timeout for remote connections
timeout = 10
```

### 4. **Create an Inventory File**

The inventory file specifies the hosts where Ansible will run commands. The default location is `/etc/ansible/hosts`.

**To edit the inventory file:**

```bash
sudo nano /etc/ansible/hosts
```

**Example Inventory File:**

```ini
# Local host
localhost ansible_connection=local

# Remote hosts
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
```

### 5. **Test Connectivity**

Ensure you can connect to the hosts defined in the inventory file:

```bash
ansible all -m ping
```

This command uses the `ping` module to test connectivity to all hosts.

### 6. **Write Your First Playbook**

Create a new YAML file for your playbook. For example, `my_playbook.yml`:

```yaml
---
- name: Ensure a package is installed
  hosts: webservers
  become: yes  # Escalate to sudo privileges
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

**Run the Playbook:**

```bash
ansible-playbook my_playbook.yml
```

### 7. **Create a Simple Role**

Roles help organize playbooks into reusable components.

**Create the Role Structure:**

```bash
ansible-galaxy init my_role
```

**Role Directory Structure:**

```
my_role/
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```

**Edit the `tasks/main.yml` File:**

```yaml
---
- name: Ensure a package is installed
  apt:
    name: "{{ package_name }}"
    state: present
```

**Use the Role in a Playbook:**

```yaml
---
- name: Use my_role
  hosts: webservers
  become: yes
  roles:
    - my_role
```

### 8. **Advanced Configuration**

For advanced usage, you might want to explore these topics:

- **Ansible Vault**: Encrypt sensitive data.
  ```bash
  ansible-vault create secret.yml
  ansible-vault edit secret.yml
  ansible-vault view secret.yml
  ```

- **Ansible Collections**: Manage and share content.
  ```bash
  ansible-galaxy collection install community.general
  ```

- **Dynamic Inventory**: Integrate with cloud providers to manage hosts dynamically.

### 9. **Common Commands**

Here are some frequently used Ansible commands:

- **List All Hosts:**

  ```bash
  ansible all --list-hosts
  ```

- **Check Syntax of a Playbook:**

  ```bash
  ansible-playbook my_playbook.yml --syntax-check
  ```

- **View Detailed Output of a Playbook:**

  ```bash
  ansible-playbook my_playbook.yml -v
  ```

- **Limit Execution to Specific Hosts or Groups:**

  ```bash
  ansible-playbook my_playbook.yml -l webservers
  ```

- **Run Playbook with a Specific Inventory File:**

  ```bash
  ansible-playbook -i my_inventory_file my_playbook.yml
  ```

### 10. **Example Playbooks**

Here are a few more example playbooks for different tasks:

**Example 1: Installing a Package**

```yaml
---
- name: Install Apache HTTP Server
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

**Example 2: Copying Files**

```yaml
---
- name: Copy Files to Remote Hosts
  hosts: webservers
  become: yes
  tasks:
    - name: Copy a file to the remote host
      copy:
        src: /path/to/local/file
        dest: /path/to/remote/destination
```

**Example 3: Managing Services**

```yaml
---
- name: Manage a Service
  hosts: webservers
  become: yes
  tasks:
    - name: Ensure Apache is started
      service:
        name: apache2
        state: started
        enabled: yes
```

### 11. **Resources and Documentation**

- **[Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)**: Official documentation for all Ansible features.
- **[Ansible GitHub Repository](https://github.com/ansible/ansible)**: Source code and issue tracker.
- **[Ansible Galaxy](https://galaxy.ansible.com/)**: Community roles and collections.

### 12. **Troubleshooting Tips**

- **Check Configuration File**: Ensure there are no syntax errors in `ansible.cfg`.
- **Verify Hosts**: Make sure the hosts in the inventory file are reachable.
- **Inspect Errors**: Review error messages for hints about what went wrong.

---

This guide should help you get started with Ansible on Ubuntu. You can adapt the instructions to fit your specific needs and expand your Ansible knowledge as you explore more advanced features.

### Additional Resources

- **[Ansible Tutorial for Beginners](https://www.redhat.com/en/topics/ansible/learn)**: A great starting point for new users.
- **[Ansible Best Practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)**: Best practices for writing and organizing your Ansible playbooks.

# [ansiblelabs]

### 1. `1_labexercise_variables.yml`

```yaml
# This playbook demonstrates how to define and use variables in Ansible.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
  - name: Dispay variable  # Display the value of the 'env' variable
    debug:
      msg: "{{env}}"  # The 'debug' module prints the value of 'env'
```

### 2. `2_labexercise_variables.yml`

```yaml
# This playbook demonstrates how to use variables and gather facts in Ansible.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  gather_facts: True  # Gather facts about the system, e.g., OS distribution
  tasks:
  - name: Dispay variable  # Display the value of the 'env' variable
    debug:
      msg: "{{env}}"  # The 'debug' module prints the value of 'env'
    
  - name: print out operating system  # Print out the operating system distribution
    debug: var=ansible_distribution  # The 'debug' module prints the fact 'ansible_distribution'
```

### 3. `3_labexcercise_TagsDemo.yml`

```yaml
# This playbook demonstrates the use of tags to run specific tasks.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  gather_facts: True  # Gather facts about the system
  tasks:
  - name: Conncetion  variable  # Display the value of the 'env' variable (Tag: task1)
    debug:
      msg: "{{env}}"
    tags:
       - task1  # Tag this task as 'task1'

  - name: Dispay variable  # Display the value of the 'env' variable (Tag: task2)
    debug:
      msg: "{{env}}"
    tags: 
       - task2  # Tag this task as 'task2'

  - name: print out operating system  # Print out the operating system distribution (Tag: task3)
    debug: var=ansible_distribution
    tags: 
      - task3  # Tag this task as 'task3'
```

### 4. `4_labexcercise_loop_withitemloopdemo.yml`

```yaml
# This playbook demonstrates how to use a loop with the 'with_items' directive.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name:  Loop Demo  # Create multiple files based on the list of items
     file:
      path: /tmp/{{item}}  # Path to the file to be created, using the item from the list
      state: touch  # Ensure the file is created
     with_items:
         - config1.j2  # Item 1
         - config2.j2  # Item 2
         - config3.j2  # Item 3
```

### 5. `5_labexcercise_loop_withnestedloopdemo1.yml`

```yaml
# This playbook demonstrates how to use a nested loop with the 'with_nested' directive.
- hosts: localhost
  vars:
    env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
    - name: Loop Demo with nested  # Create multiple files based on nested lists
      file:
        path: "/tmp/{{ item[0] }}-{{ item[1] }}"  # Path combining items from both lists
        state: touch  # Ensure the file is created
      with_nested:
        - [ 'config1.j2', 'config2.j2', 'config3.j2' ]  # List of config files
        - [ 'file1', 'file2', 'file3' ]  # List of file names
```

### 6. `6_labexcercise_loop_withnestedloopdemo.yml`

```yaml
# This playbook demonstrates a nested loop to display combinations of two lists.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name: Nested Loop Demo  # Display combinations of two lists
     debug:
         msg: "{{item[0]}}-{{item[1]}}"  # Print the combination of items from both lists
     with_nested:
      - [ 'Section-1', 'Section-2' ]  # List of sections
      - [ 'Part-I', 'Part-II', 'Part-III' ]  # List of parts
```

### 7. `7_labexcercise_registerkeywords_registerkeyworddemo.yml`

```yaml
# This playbook demonstrates how to use the 'register' keyword to store the result of a task.
- hosts: localhost
  tasks:
  - name: Register Keyword Demo  # Find all .txt files in a specific directory
    shell: "find *.txt"
    args:
      chdir: "/tmp/dirforexample/"  # Change to the directory for the 'find' command
    register: find_output  # Register the output of the 'find' command to 'find_output'

  - debug:
      var: find_output  # Display the content of 'find_output' variable
```

### 8. `8_labexcercise_when_whenstatementdemo.yml`

```yaml
# This playbook demonstrates how to use the 'when' statement for conditional tasks.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name: Checking for Ubuntu system type and creating a file if its true  # Create a file if the OS is Ubuntu
     file:
      path: /tmp/UbuntuDistribution
      state: touch  # Ensure the file is created
     when: ansible_distribution =="Ubuntu"  # Condition to check if the OS is Ubuntu

   - name: Checking for Centos system type and creating a file if its true  # Create a file if the OS is CentOS
     file:
       path: /tmp/Centos
       state: touch  # Ensure the file is created
     when: ansible_distribution =="Centos"  # Condition to check if the OS is CentOS
```
### 9. 'disk_space_checker.yml
# This playbook demonstrates how to execute the `du -h` command to display disk usage and register the output.

```yaml
- hosts: localhost  # Target the localhost for this playbook
  tasks:
    - name: Execute du -h command  # Run the 'du -h' command to display disk usage in a human-readable format
      shell: "du -h /tmp"  # Command to be executed; adjust path if needed
      register: du_output  # Register the output of the command to the 'du_output' variable

    - name: Display disk usage output  # Display the output of the 'du -h' command
      debug:
        var: du_output  # Show the content of the 'du_output' variable
```

### Explanation of the Playbook

1. **`- hosts: localhost`**: The playbook targets the `localhost` for execution.

2. **`tasks:`**: Defines the list of tasks to be executed on the target hosts.

3. **`- name: Execute du -h command`**:
   - **Purpose**: Runs the `du -h` command to display disk usage in a human-readable format.
   - **Module**: `shell`
   - **Command**: `du -h /tmp` (You can change `/tmp` to any directory you want to check disk usage for.)
   - **Register**: The result of the command is stored in the `du_output` variable.

4. **`- name: Display disk usage output`**:
   - **Purpose**: Displays the output of the `du -h` command.
   - **Module**: `debug`
   - **Variable**: `du_output` contains the command’s output, which is displayed.

### Additional Comments

If you want to adjust the playbook to perform different actions or include more advanced features, you can explore the following options:

- **Add Conditional Logic**: Use the `when` statement to run the command only if certain conditions are met.

  ```yaml
  - name: Execute du -h command only if the directory exists
    shell: "du -h /tmp"
    register: du_output
    when: ansible_facts['ansible_mounts'][0]['mount'] == "/tmp"  # Example condition
  ```

- **Use Different Modules**: Instead of `shell`, you could use the `command` module for simpler commands.

  ```yaml
  - name: Execute du -h command
    command: "du -h /tmp"
    register: du_output
  ```

- **Include a Loop**: If you want to run `du -h` on multiple directories, you can use a loop.

  ```yaml
  - name: Execute du -h command on multiple directories
    shell: "du -h {{ item }}"
    with_items:
      - /tmp
      - /var
      - /home
    register: du_output
  ```

Feel free to adapt these suggestions to fit your needs. Here's a more detailed playbook with these advanced features:

### Advanced Example: `9_labexcercise_du_command_advanced.yml`

```yaml
# This playbook demonstrates advanced features like conditionals, loops, and different modules.

- hosts: localhost  # Target the localhost for this playbook
  gather_facts: True  # Gather system facts

  tasks:
    - name: Check if /tmp directory exists  # Check for the existence of the /tmp directory
      stat:
        path: /tmp
      register: tmp_stat  # Register the result of the stat command

    - name: Execute du -h command on /tmp if it exists  # Run 'du -h' only if /tmp directory exists
      shell: "du -h /tmp"
      register: du_output
      when: tmp_stat.stat.exists  # Condition to check if /tmp exists

    - name: Display disk usage output  # Display the output of the 'du -h' command
      debug:
        var: du_output  # Show the content of the 'du_output' variable

    - name: Execute du -h command on multiple directories  # Example of using a loop
      shell: "du -h {{ item }}"
      with_items:
        - /tmp
        - /var
        - /home
      register: du_output_loop  # Register the results from multiple directories

    - name: Display disk usage output for multiple directories  # Display the output of the loop command
      debug:
        var: du_output_loop  # Show the content of the 'du_output_loop' variable
```

This advanced playbook includes:
- Checking the existence of a directory before running the command.
- Running the `du -h` command on multiple directories.
- Displaying results for both single and multiple commands.

You can choose between the basic and advanced examples depending on your requirements.


