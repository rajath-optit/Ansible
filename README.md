
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


