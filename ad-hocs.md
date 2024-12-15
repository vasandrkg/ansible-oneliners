### Print the inventory file in json format
```bash
ansible-inventory -i inventories/some_env/hosts --list;
```

### Сheck that hosts are available
```bash
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user' \
        --extra-vars 'ansible_password=some_pass';
```

### Сheck that particular hosts are available
##### we are using inline inventory and not inventory file 
##### pay attention to trailing comma for the list of hosts, it's important
```bash
ansible --inventory "host1.example.com,host2.example.com,host3.example.com," all \
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
