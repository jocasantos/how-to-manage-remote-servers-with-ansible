# How to manage remote servers with Ansible


### Create two EC2 instances (Linux Ubuntu), and connect to the first instance (master) from your terminal

> The first instance will be the master and the second will be the target server. We will configure the target server from the master one

1. Update packages
```
sudo apt update
```
2. Install Ansible
```
sudo apt install ansible
```
3. Make sure it is installed by checking the version
```
ansible --version
```


### Make the passwordless connection between servers using SSH

1. Generate a public/private rsa key pair in your master server
```
ssh-keygen
```
2. Copy your public key
```
cat /home/ubuntu/.ssh/id_rsa.pub
```
3. Connect to your second instance and allow the master server connection
    - Generate a public/private rsa key pair in your target server
    ```
    ssh-keygen
    ```
    - Copy the master's public key in the `authorized_keys`
    ```
    vim ~/.ssh/authorized_keys
    ```
4. Test the passwordless connection between servers
    - Come back to your master server and try to connect to your target server
    ```
    ssh "target private ip adress" 
    ``` 
> You can look for your target instance private ip adress in your AWS console (EC2 instance)
5. If you are now in your target server, the connection between master and target servers is now possible 🎉  



### Run some simple tasks in your target server with "adhoc commands"

1. Add the target private ip adress in your `inventory`
    - Make sure you are in ansible directory 
    ```
    cd ansible
    ```
    - Add the ip to `inventory`. (If you want you can divide various servers in different sets, just put the name `[exampleservers]` and paste the ips under)
    ```
    vim inventory
    ```
2. Run adhoc commands
```
ansible -i inventory all -m "shell" -a "touch test"
```
> -i is to provide the path to the inventory file (single ip, all, or set of servers), -m is the module you gone use (shell command), and -a is the arguments (create a file named test)



### Run tasks with "playbooks"

> Lets install Nginx and restart Nginx in our targets servers
1. Create the playbook 
```
vim first-playbook.yml
```
2. Insert the tasks 
```
---
- name: Install and Start Nginx
  hosts: all #ip targets
  become: root #admin privileges

  tasks: 
    - name: Install Nginx
      apt:
        name: Nginx
        state: present
    - name: Start Nginx
      service: 
        name: nginx 
        state: started         
```
3. Execute the playbook
```
ansible-playbook -i inventory first-playbook.yml
``` 


### Congratulations! You learn how to manage remote servers with Ansible 🎉

>  If you want to write some complex playbooks I advise learn about Roles in Ansible ``ansible-galaxy role init RoleName``