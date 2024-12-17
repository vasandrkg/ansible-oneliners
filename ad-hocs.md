### Print the inventory file in json format
```bash
ansible-inventory --inventory inventories/some_env/hosts --list;
```

### List hosts from the particular group from the inventory file
```bash
ansible SOME_GROUP --inventory inventories/some_env/hosts --list-hosts;
```



### Сheck that hosts are available
```bash
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### Using inline inventory and not inventory file
```bash
ansible --inventory "host1.example.com,host2.example.com,host3.example.com," all \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### Run ansible ad-hoc command without inventory file
```bash
ansible all --inventory "host1.example.com,host2.example.com,host3.example.com," \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```


### Run some shell command on the hosts
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker images | grep some_image_name" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### List containers with particular name (consul-client for example) using ansible shell module
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker container list --all --filter name='consul-client'" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### List containers with particular name (consul-client) and another name (elastic) using multiple filters
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

### Start docker containers using docker_container module
```sh
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name docker_container \
        --args "name=some_container_name state=started" \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### Run particular ansible ad-hoc command host by host  
```sh
for host in $(ansible-inventory --inventory inventories/all/hosts --list | grep "some_host_pattern_goes_here" | awk '{print $1}' | tr -d '"|,|:' | sort | uniq); 
        do
                ansible $host --inventory inventories/all/hosts --module-name command --args "your_command_here";
        done
```

### Run single ansible ad-hoc command in order host by host
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


