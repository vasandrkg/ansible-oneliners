# check that hosts are available
```shell
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name ping \
        --extra-vars 'ansible_user=some_user'
        --extra-vars 'ansible_password=some_pass';
```

# run some shell command
```bash
ansible --inventory inventories/some_env/hosts some_group_from_hosts_file \
        --become \
        --module-name shell \
        --args "docker images | grep some_image_name" \
        --extra-vars 'ansible_user=some_user'
        --extra-vars 'ansible_password=some_pass';
```
