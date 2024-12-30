#### The most complicated and beautiful Ansible ad-hoc command I've been using on my Mac so far.
```bash
sudo docker pull alpine/ansible:latest;

cd /Users/some_user/go/src/some_dir_with_ansible_stuff;

sudo docker run -it --rm \
           --name ansible-ad-hoc \
           --volume /Users/some_user/go/src/some_dir_with_ansible_stuff:/ansible \
           --volume /Users/some_user/.ssh/some_dir_with_private_ssh_keys:/ansible/.ssh \
           --env ENV_POSTGRES_DB_NAME='some_postgres_db_name' \
           --env ENV_POSTGRES_PORT='5432' \
           --env-file /Users/some_user/go/src/some_dir_with_ansible_stuff/env.list.file \
           --workdir /ansible \
           alpine/ansible:latest \
           /bin/bash -c "export ANSIBLE_HOST_KEY_CHECKING=False && \
           ansible --inventory ./inventories/all/hosts all \
           --become \
           --private-key=./.ssh/some_private_ssh_key \
           --module-name shell \
           --args \"uptime\" \
           --extra-vars 'ansible_python_interpreter=/usr/bin/python3' \
           --extra-vars 'ansible_user=some_user' \
           --extra-vars 'ansible_password=some_pass'"

```


#### Print the inventory file in json format
```bash
ansible-inventory --inventory inventories/some_env/hosts --list;
```

#### List hosts from the particular group from the inventory file
```bash
ansible SOME_GROUP --inventory inventories/some_env/hosts --list-hosts;
```

#### Сheck that hosts are available using plain text .ini inventory file and ansible.cfg config file
```bash
ansible --inventory inventory.ini all \
        --become \
        --module-name ping;

ansible --inventory inventory.ini some_group_from_inventory_ini_file \
        --become \
        --module-name ping;
```


#### Сheck that hosts are available
```bash
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### Using inline inventory and not inventory file
```bash
ansible --inventory "host1.example.com,host2.example.com,host3.example.com," all \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### Run ansible ad-hoc command without inventory file
```bash
ansible all --inventory "host1.example.com,host2.example.com,host3.example.com," \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```


#### Run some shell command on the hosts
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker images | grep some_image_name" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### List containers with particular name (consul-client for example) using ansible shell module
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker container list --all --filter name='consul-client'" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### List containers with particular name (consul-client) and another name (elastic) using multiple filters
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker container list --all \
        --filter name='consul-client' \
        --filter name='elastic'" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### Start docker containers using docker_container module
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name docker_container \
        --args "name=some_container_name state=started" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

#### Run particular ansible ad-hoc command host by host  
```sh
for host in $(ansible-inventory --inventory inventories/all/hosts --list | grep "some_host_pattern_goes_here" | awk '{print $1}' | tr -d '"|,|:' | sort | uniq); 
        do
                ansible $host --inventory inventories/all/hosts --module-name command --args "your_command_here";
        done
```

#### Run single ansible ad-hoc command in order host by host
#### Please pay attention to trailing comma after $host, in that way ansible knows it's an inventory file!     
```sh
for host in $(ansible-inventory --inventory inventories/all/hosts --list | grep "some_host_pattern" | awk '{print $1}' | tr -d '"|,|:' | sort | uniq); 
        do
                ansible --inventory "$host," all \
                        --become \
                        --module-name ping \
                        --extra-vars 'ansible_user=some_user' \
                        --extra-vars 'ansible_password=some_path';                    
        done
```


#### Run some Ansible ad-hoc command in Ansible Docker container on a Linux host
```sh
sudo docker pull alpine/ansible:latest;

cd /home/some_user/go/src/some_ansible_dir;

sudo docker run -it --rm --name ansible-ad-hoc --volume /home/some_user/go/src/some_ansible_dir:/ansible --volume /home/some_user/.ssh/some_ssh_dir:/ansible/.ssh alpine/ansible:latest /bin/bash -c "export ANSIBLE_HOST_KEY_CHECKING=False && ansible --inventory ansible/inventories/all/hosts all --become --private-key=/ansible/.ssh/some_private_ssh_key --module-name shell --args \"uptime\"";

```

#### Run some Ansible ad-hoc command in Ansible Docker container on a Mac host
```sh
sudo docker pull alpine/ansible:latest;

cd /Users/some_user/go/src/some_ansible_dir;

sudo docker run -it --rm --name ansible-ad-hoc --volume /Users/some_user/go/src/some_ansible_dir:/ansible --volume /Users/some_user/.ssh/some_ssh_dir_with_private_keys:/ansible/.ssh alpine/ansible:latest /bin/bash -c "export ANSIBLE_HOST_KEY_CHECKING=False && ansible --inventory ansible/inventories/all/hosts all --become --private-key=/ansible/.ssh/some_private_ssh_key --module-name shell --args \"uptime\"";

```

#### Run some Ansible ad-hoc command in Ansible Docker container on a Mac host
```sh
sudo docker pull alpine/ansible:latest;

cd /Users/some_user/go/src/some_ansible_dir;

sudo docker run -it --rm --name ansible-ad-hoc --volume /Users/some_user/go/src/some_ansible_dir:/ansible --volume /Users/some_user/.ssh/some_ssh_dir_with_private_keys:/ansible/.ssh alpine/ansible:latest /bin/bash -c "export ANSIBLE_HOST_KEY_CHECKING=False && ansible --inventory ansible/inventories/all/hosts all --become --private-key=/ansible/.ssh/some_private_ssh_key --module-name shell --args \"uptime\" --extra-vars ansible_python_interpreter='/usr/bin/python3'";

```

