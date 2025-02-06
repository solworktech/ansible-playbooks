## Ansible playbooks

This repo includes various useful playbooks for erecting and updating Linux machines. Some playbooks are specific to
certain distributions (and their derivatives). 

## General FS structure

- `env`: directory to hold ENV specific data (such as hosts)
- `files`: static files (such as public RSA keys)  
- `group_vars`: vars used in different playbooks/tasks. **Secrets must only be kept in vaulted files!**  
- `playbooks`: playbooks should be organised in sub-directories based on their function/project they pertain to
- `roles`: roles are a collection of tasks that are grouped under a common name

## Ansible Vault

Ansible Vault is a feature of ansible that allows you to keep sensitive data such as passwords or keys in encrypted files, rather than as plaintext in playbooks or roles. A CLI tool `ansible-vault` is used to create and edit files.

For more details, see the [Ansible Vault docs](https://docs.ansible.com/ansible/2.8/user_guide/vault.html#ansible-vault)

Set `ANSIBLE_VAULT_PASSWORD_FILE` thusly, where `/path/to/.vault_password_file` is a text file with the vault's passwd:
```sh
export ANSIBLE_VAULT_PASSWORD_FILE=/path/to/.vault_password_file
```

## Dependencies

Some Ansible playbooks in this repo require the installation of additional modules (which are not part of ansible-core),
before running anything, please invoke the below command to install them:

```sh
ansible-galaxy collection install community.general ansible.posix amazon.aws prometheus.prometheus community.crypto
```

## ENV vars

The default role path is: `{{ ANSIBLE_HOME ~ "/roles:/usr/share/ansible/roles:/etc/ansible/roles" }}`

Depending on where you intend to run `ansible-playbook` from, you may need to export `ANSIBLE_ROLES_PATH` so that it
points to `ROOT_DIR/roles`.

For more info, see the [Ansible Configuration doc](https://docs.ansible.com/ansible/latest/reference_appendices/config.html#default-roles-path)

## Running playbooks

- Add the host to `env/hosts.ini`, set `ANSIBLE_VAULT_PASSWORD_FILE` as per the `Ansible Vault` section above 
- Edit `group_vars/your_creds.yml` and set your username and path to PEM key
- Invoke:

```sh
ansible-playbook playbooks/path/to/playbook.yml -i env/hosts.ini --extra-vars "variable_host=$HOST_NAME_HERE"
```

## pre-commit hooks

Install the `pre-commit` util:
```sh
pip install pre-commit
```

Generate `.git/hooks/pre-commit`:
```sh
pre-commit install
```

Following that, these tests will run every time you invoke `git commit`:

```
Ansible-lint.............................................................Passed
Detect hardcoded secrets.................................................Passed
```

To manually run all tests on all repo files, invoke:

```sh
pre-commit run --all-files
```


