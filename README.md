starting with the Ansible setup and installation, followed by examples from the lab directory, and concluding with connecting Ansible to Jenkins and testing the build.

---

# **Ansible Setup and Usage Guide**

## **Table of Contents**
1. [Ansible Setup and Installation](#ansible-setup-and-installation)
2. [Lab Exercises](#lab-exercises)
    - [1. Variable Demonstration](#1-variable-demonstration)
    - [2. Variables and Facts](#2-variables-and-facts)
    - [3. Tags Demo](#3-tags-demo)
    - [4. Loop with Items Demo](#4-loop-with-items-demo)
    - [5. Nested Loop Demo 1](#5-nested-loop-demo-1)
    - [6. Nested Loop Demo 2](#6-nested-loop-demo-2)
    - [7. Register Keyword Demo](#7-register-keyword-demo)
    - [8. Conditional Tasks](#8-conditional-tasks)
    - [9. Disk Space Checker](#9-disk-space-checker)
3. [Connecting Ansible to Jenkins and Testing Build](#connecting-ansible-to-jenkins-and-testing-build)

---
## **Ansible Setup and Installation**

### **Step 1: Install Ansible**

#### **For Ubuntu/Debian:**
```sh
sudo apt update
sudo apt install ansible -y
```

#### **For CentOS/RHEL:**
```sh
sudo yum install epel-release -y
sudo yum install ansible -y
```

### **Step 2: Verify Installation**
```sh
ansible --version
```

### **Step 3: Set Up Ansible for Non-Sudo User on Ubuntu**

To allow a non-sudo user to use Ansible without requiring sudo privileges, you can add the user to the Ansible configuration. Follow these steps:

1. **Create a Configuration Directory** (if it doesn't exist):
    ```sh
    mkdir -p ~/.ansible
    ```

2. **Create or Edit the Ansible Configuration File**:
    Create a file named `ansible.cfg` in the `~/.ansible` directory:
    ```sh
    nano ~/.ansible/ansible.cfg
    ```

3. **Add Configuration to ansible.cfg**:
    Add the following configuration to allow the non-sudo user to run Ansible playbooks:
    ```ini
    [defaults]
    inventory = ~/.ansible/hosts

    [privilege_escalation]
    become = True
    become_method = sudo
    become_user = root
    ```

4. **Create an Inventory File**:
    Create a file named `hosts` in the `~/.ansible` directory:
    ```sh
    nano ~/.ansible/hosts
    ```

5. **Add Localhost to Inventory**:
    Add the following line to the `hosts` file to include the localhost in the inventory:
    ```ini
    localhost ansible_connection=local
    ```

6. **Ensure Permissions**:
    Ensure that the `ansible.cfg` and `hosts` files are readable by your user:
    ```sh
    chmod 644 ~/.ansible/ansible.cfg
    chmod 644 ~/.ansible/hosts
    ```

7. **Test Ansible Configuration**:
    Run a simple Ansible command to ensure everything is set up correctly:
    ```sh
    ansible all -m ping
    ```

#### Expected Output:
```sh
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

By following these steps, you can configure Ansible to be used by a non-sudo user on an Ubuntu system without affecting the local machine or requiring a virtual environment.

---

## **Lab Exercises**

### **1. Variable Demonstration**

**File: `1_labexercise_variables.yml`**

```yaml
# This playbook demonstrates how to define and use variables in Ansible.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
  - name: Display variable
    debug:
      msg: "{{env}}"
```


### **2. Variables and Facts**

**File: `2_labexercise_variables.yml`**

```yaml
# This playbook demonstrates how to use variables and gather facts in Ansible.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  gather_facts: True  # Gather facts about the system, e.g., OS distribution
  tasks:
  - name: Display variable
    debug:
      msg: "{{env}}"
    
  - name: Print out operating system
    debug:
      var: ansible_distribution
```

### **3. Tags Demo**

**File: `3_labexercise_tagsdemo.yml`**

```yaml
# This playbook demonstrates the use of tags to run specific tasks.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  gather_facts: True  # Gather facts about the system
  tasks:
  - name: Display variable (Tag: task1)
    debug:
      msg: "{{env}}"
    tags:
       - task1

  - name: Display variable (Tag: task2)
    debug:
      msg: "{{env}}"
    tags: 
       - task2

  - name: Print out operating system (Tag: task3)
    debug:
      var: ansible_distribution
    tags: 
      - task3
```

### **4. Loop with Items Demo**

**File: `4_labexercise_loop_withitemloopdemo.yml`**

```yaml
# This playbook demonstrates how to use a loop with the 'with_items' directive.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name: Loop Demo
     file:
      path: /tmp/{{item}}
      state: touch
     with_items:
         - config1.j2
         - config2.j2
         - config3.j2
```

### **5. Nested Loop Demo 1**

**File: `5_labexercise_loop_withnestedloopdemo1.yml`**

```yaml
# This playbook demonstrates how to use a nested loop with the 'with_nested' directive.
- hosts: localhost
  vars:
    env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
    - name: Loop Demo with nested
      file:
        path: "/tmp/{{ item[0] }}-{{ item[1] }}"
        state: touch
      with_nested:
        - [ 'config1.j2', 'config2.j2', 'config3.j2' ]
        - [ 'file1', 'file2', 'file3' ]
```

### **6. Nested Loop Demo 2**

**File: `6_labexercise_loop_withnestedloopdemo.yml`**

```yaml
# This playbook demonstrates a nested loop to display combinations of two lists.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name: Nested Loop Demo
     debug:
         msg: "{{item[0]}}-{{item[1]}}"
     with_nested:
      - [ 'Section-1', 'Section-2' ]
      - [ 'Part-I', 'Part-II', 'Part-III' ]
```

### **7. Register Keyword Demo**

**File: `7_labexercise_registerkeywords_registerkeyworddemo.yml`**

```yaml
# This playbook demonstrates how to use the 'register' keyword to store the result of a task.
- hosts: localhost
  tasks:
  - name: Register Keyword Demo
    shell: "find *.txt"
    args:
      chdir: "/tmp/dirforexample/"
    register: find_output

  - name: Show output
    debug:
      var: find_output
```

### **8. Conditional Tasks**

**File: `8_labexercise_when_whenstatementdemo.yml`**

```yaml
# This playbook demonstrates how to use the 'when' statement for conditional tasks.
- hosts: localhost
  vars:
     env: dev  # Define a variable 'env' with the value 'dev'
  tasks:
   - name: Checking for Ubuntu system type and creating a file if true
     file:
      path: /tmp/UbuntuDistribution
      state: touch
     when: ansible_distribution == "Ubuntu"

   - name: Checking for CentOS system type and creating a file if true
     file:
       path: /tmp/Centos
       state: touch
     when: ansible_distribution == "CentOS"
```

### **9. Disk Space Checker**

**File: `disk_space_checker.yml`**

```yaml
# This playbook demonstrates how to execute the `du -h` command to display disk usage and register the output.
- hosts: localhost  # Target the localhost for this playbook
  tasks:
    - name: Execute du -h command  # Run the 'du -h' command to display disk usage in a human-readable format
      shell: "du -h /tmp"  # Command to be executed; adjust path if needed
      register: du_output  # Register the output of the command to the 'du_output' variable

    - name: Display disk usage output  # Display the output of the 'du -h' command
      debug:
        var: du_output  # Show the content of the 'du_output' variable
```

### **Explanation of the Playbook**

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

### **Additional Comments**

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

### **Advanced Example: `9_labexcercise_du_command_advanced.yml`**

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

---

## **Connecting Ansible to Jenkins and Testing Build**

### **Step 1: Configure Jenkins Job**

1. **Install Ansible Plugin**:
    - Go to Jenkins Dashboard -> Manage Jenkins -> Manage Plugins.
    - Search for "Ansible" in the Available tab and install it.

2. **Create a New Job**:
    - Go to Jenkins Dashboard -> New Item.
    - Enter an item name and select "Freestyle project", then click OK.

3. **Configure Job**:
    - In the job configuration page, scroll down to the "Build" section.
    - Click on "Add build step" and select "Invoke Ansible Playbook".
    - Configure the Ansible Playbook settings:
        - **Playbook Path**: Specify the path to your Ansible playbook file (e.g., `disk_space_checker.yml`).
        - **Inventory**: Specify the path to your inventory file or use `localhost` for local execution.

4. **Save and Run Job**:
    - Save the job configuration.
    - Go back to the job's main page and click "Build Now" to run the job.

### **Step 2: Test Build**

1. **Check Build Status**:
    - Go to the job's main page and check the build status in the Build History.
    - Click on the build number to see the build details and console output.

2. **Verify Ansible Output**:
    - Check the console output to verify that the Ansible playbook ran successfully.
    - Ensure that the output of the `du -h` command or any other task results are displayed as expected.

By following these steps, you will have successfully connected Ansible to Jenkins and tested the build to verify that your playbooks execute correctly within the CI/CD pipeline.
