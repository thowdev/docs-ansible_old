# 1. Ansible ecosystem - Information, tips, best practices

<!-- TOC -->

- [1. Ansible ecosystem - Information, tips, best practices](#1-ansible-ecosystem---information-tips-best-practices)
  - [1.1. Ansible](#11-ansible)
    - [1.1.1. Installation](#111-installation)
      - [1.1.1.1. Global installation](#1111-global-installation)
      - [1.1.1.2. Local installation](#1112-local-installation)
    - [1.1.2. Inventory](#112-inventory)
    - [1.1.3. Roles](#113-roles)
    - [1.1.4. Plugins](#114-plugins)
  - [1.2. Ansible Galaxy](#12-ansible-galaxy)
    - [1.2.1. Create new ansible role](#121-create-new-ansible-role)
    - [1.2.2. List installed ansible roles](#122-list-installed-ansible-roles)
    - [1.2.3. Download ansible roles from different sources](#123-download-ansible-roles-from-different-sources)
  - [1.3. Molecule - Ansible Testing](#13-molecule---ansible-testing)
    - [1.3.1. Installation via pip](#131-installation-via-pip)
    - [1.3.2. Create new ansible role with molecule](#132-create-new-ansible-role-with-molecule)
    - [1.3.3. Molecule Scenarios](#133-molecule-scenarios)
  - [1.4. Useful links/information/discussions/questions/decisions:](#14-useful-linksinformationdiscussionsquestionsdecisions)
  - [1.5. References](#15-references)

<!-- /TOC -->

## 1.1. Ansible

### 1.1.1. Installation

#### 1.1.1.1. Global installation

```bash
# Ansible installation
pip install ansible

# Ansible upgrade
pip install --upgrade ansible
````

#### 1.1.1.2. Local installation

```bash
# Example installation in an independent local virtual environment
python -m venv venv-ansible-installation
source venv-ansible-installation/bin/activate
pip install --upgrade pip
pip install ansible
```

### 1.1.2. Inventory

List all hosts of playbook/inventory file (default location: /etc/ansible/
hosts):

```bash
ansible-inventory -i hosts --graph
ansible-inventory -i hosts --list
```

### 1.1.3. Roles

- Role directory structure
  - https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html
- Dependencies to other roles defined in

  ```bash
  # cat <path-to-roles>/<role_name>/meta/main.yml:
  - dependencies: []
  ```

- [Create roles with ansible](#create-new-ansible-role)
- [Create roles with molecule](#create-new-ansible-role-with-molecule)

### 1.1.4. Plugins

- Get list of available plugins:\
  `$ ansible-doc -t lookup -l`
- List of plugin types (and therefore directory names for development)
  - https://docs.ansible.com/ansible/latest/dev_guide/developing_locally.html#adding-a-plugin-locally
- Lookup plugins:
  - https://docs.ansible.com/ansible/latest/plugins/lookup.html

## 1.2. Ansible Galaxy

### 1.2.1. Create new ansible role

- The "ansible way":
https://galaxy.ansible.com/docs/contributing/creating_role.html

  ```bash
  # ansible-galaxy init --init-path <path-to-roles> <role_name>
  $ ansible-galaxy init --init-path . ar-common
  $ ls -la ./ar-common
      8 -rw-r--r--  1 user  staff   1,3K 15 Aug 13:13 .travis.yml
      8 -rw-r--r--  1 user  staff   1,3K 15 Aug 13:13 README.md
      0 drwxr-xr-x  3 user  staff    96B 15 Aug 13:13 defaults/
      0 drwxr-xr-x  2 user  staff    64B 15 Aug 13:13 files/
      0 drwxr-xr-x  3 user  staff    96B 15 Aug 13:13 handlers/
      0 drwxr-xr-x  3 user  staff    96B 15 Aug 13:13 meta/
      0 drwxr-xr-x  3 user  staff    96B 15 Aug 13:13 tasks/
      0 drwxr-xr-x  2 user  staff    64B 15 Aug 13:13 templates/
      0 drwxr-xr-x  4 user  staff   128B 15 Aug 13:13 tests/
      0 drwxr-xr-x  3 user  staff    96B 15 Aug 13:13 vars/
  ```

- The "molecule way":
https://molecule.readthedocs.io/en/latest/getting-started.html#creating-a-new-role

  ```bash
  # molecule init role my-new-role --driver-name docker 
  $ molecule init role ar-common-molecule --driver-name docker 
  $ ls -la ar-common-molecule 
      8 -rw-r--r--  1 thwi  staff   539B Dec 23 12:19 .travis.yml
      8 -rw-r--r--  1 thwi  staff   598B Dec 23 12:19 .yamllint
      8 -rw-r--r--  1 thwi  staff   1.3K Dec 23 12:19 README.md
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 defaults/
      0 drwxr-xr-x  2 thwi  staff    64B Dec 23 12:19 files/
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 handlers/
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 meta/
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 molecule/
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 tasks/
      0 drwxr-xr-x  2 thwi  staff    64B Dec 23 12:19 templates/
      0 drwxr-xr-x  4 thwi  staff   128B Dec 23 12:19 tests/
      0 drwxr-xr-x  3 thwi  staff    96B Dec 23 12:19 vars/
  ```

### 1.2.2. List installed ansible roles

- `$ ansible-galaxy list`

### 1.2.3. Download ansible roles from different sources

- https://docs.ansible.com/ansible/latest/reference_appendices/galaxy.html?highlight=role#installing-roles
- https://galaxy.ansible.com/docs/using/installing.html#installing-multiple-roles-from-a-file

- Install roles from Ansible Galaxy\
`$ ansible-galaxy install <role.name>`  

- Install roles from <requirements.yml>\
`$ ansible-galaxy install --roles-path <path-to-roles> -r <requirements.yml>`  

- Create yaml file <requirements.yml>

  ```bash
  ---
  # from Github
  - src: https://github.com/bennojoy/nginx
  # from galaxy
  - src: yatesr.timezone
  ...
  ```

## 1.3. Molecule - Ansible Testing

- https://molecule.readthedocs.io
- But keep in mind that Ansible itself says that an extra testing framework isn't necessary:
  https://docs.ansible.com/ansible/latest/reference_appendices/test_strategies.html#conclusion 
  > Ansible believes you should not need another framework to validate basic things of your infrastructure is true. This is the case because Ansible is an order-based system that will fail immediately on unhandled errors for a host, and prevent further configuration of that host. This forces errors to the top and shows them in a summary at the end of the Ansible run.

### 1.3.1. Installation via pip

- https://molecule.readthedocs.io/en/latest/installation.html
- > It is highly recommended that you install molecule in a virtual environment!
- Install the following molecule drivers

  ```bash
  pip install --upgrade setuptools
  pip install "molecule[docker]"
  pip install "molecule[lint]"
  pip install "molecule[podman]"
  # same than
  # pip install "molecule[docker,lint,podman]
  ```

### 1.3.2. Create new ansible role with molecule

- https://molecule.readthedocs.io/en/latest/getting-started.html#creating-a-new-role
- > Molecule uses galaxy under the hood to generate conventional role layouts

  ```bash
  # molecule init role my-new-role --driver-name docker 
  $ molecule init role ar-common-molecule --driver-name docker 
  ```

### 1.3.3. Molecule Scenarios

- https://molecule.readthedocs.io/en/latest/getting-started.html#molecule-scenarios
- Every folder in `ar-common-molecule/molecule/<folder>` represents a molecule scenario
- And every scenario contains the following files:

  - `INSTALL.rst`: Info und install instructions
  - `molecule.yml`: Central configuration entrypoint for molecule. Molecule invokes this playbook with `ansible-playbook` and run it against an instance created by the driver
  - `verify.yml`: is the Ansible file used for testing as Ansible is the default Verifier.

## 1.4. Useful links/information/discussions/questions/decisions:

- Best Practices:
  - https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html

- YAML Syntax:
  - https://yaml.org/refcard.html
  - https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html
- Ansible lookups: vars vs. facts
  - https://opensolitude.com/2015/05/27/ansible-lookups-variables-vs-facts.html

[vars_vs_facts]: https://opensolitude.com/2015/05/27/ansible-lookups-variables-vs-facts.html 
  
## 1.5. References

- Ansible Docs - https://docs.ansible.com/
- Ansible Galaxy Docs - https://galaxy.ansible.com/docs/
- Ansible Galaxy User Guide - https://docs.ansible.com/ansible/latest/galaxy/user_guide.html
- Molecule - Ansible Testing - https://molecule.readthedocs.io