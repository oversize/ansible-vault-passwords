# Ansible Vault Passwords

We can specify the vault password in the ansible.cfg or via the command line.
But i want to show how else one can provide that password to ansible. And
maybe later add on to that by using some external source to grab the password
from.

... You need to have ansible installed!

## A file with the password

The env variable `ANSIBLE_VAULT_PASSWORD_FILE` can provide a file that holds the
password.

```bash
echo "supersecret" > /tmp/vaultpass
export ANSIBLE_VAULT_PASSWORD_FILE=/tmp/vaultpass
ansible-playbook playbooks/simple.yml
```

Sources:
    https://docs.ansible.com/ansible/latest/reference_appendices/config.html#envvar-ANSIBLE_VAULT_PASSWORD_FILE

## An executable file

If the file pointed to by `ANSIBLE_VAULT_PASSWORD_FILE` is executable, ansible
will execute it and grab the password from its stdout.

Create a script. I chose `~/.local/bin/vault-pass` but it does not really need
to be in $PATH since ansible requires the full path anyhow. Make it executable!

```bash
#!/bin/bash
echo $MYSECRET_VAULT_PASSWORD
```

Point ansible to that executable script as its ANSIBLE_VAULT_PASSWORD_FILE

```bash
export ANSIBLE_VAULT_PASSWORD_FILE=~/.local/bin/vault-pass
```

Now, when ansible needs a vault password it will execute the script which will
read `MYSECRET_VAULT_PASSWORD` env var and print it to sdtout.

```bash

export MYSECRET_VAULT_PASSWORD="supersecret"
ansible-playbook playbooks/simple.yml
```
