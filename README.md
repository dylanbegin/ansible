> [!WARNING]
> This README is still under development!

# ![logo](https://raw.githubusercontent.com/walkxcode/dashboard-icons/07a06d893e901fda965ba10f39d7aa7a3a18ea0d/svg/ansible.svg) Ansible Role for Core Services
> [!IMPORTANT]
> This repo is built for my own environment so please review all configurations to verify compatibility!

This repo provides all the configurations for setting up the core services after the `terraform-core` role is ocompleted.

> [!TIP]
> This repo is part of my IaC automation series. If you are building this in mind please follow my repo's in the order below.

1.  [terraform-iso-get](https://github.com/dylanbegin/terraform-iso-get)
1.  [packer](https://github.com/dylanbegin/packer)
1.  [terraform-core](https://github.com/dylanbegin/terraform-core)
1.  *you are here* [ansible](https://github.com/dylanbegin/ansible)
1.  [terraform-talos](https://github.com/dylanbegin/terraform-talos)
1.  [k8s-apps](https://github.com/dylanbegin/k8s-apps)

# Build Your Secrets File
Keeping in best practice, this repo does not contain any sensitive information. You will need to create a directory outside of this git repo on a properly encrypted disk/usb to save the secrets file. Below is the template needed for the file which needs to be named `ansible.cfg`.
```ini
[defaults]
debug = false
no_log = true
log_path = ~/.ansible/log.txt
inventory = /path-to/secrets/hosts.ini
private_key_file = /path-to/ssh-priv-key
host_key_checking = false
interpreter_python = auto_silent

[privilege_escalation]
become_method = doas #or sudo

[ssh_connection]
scp_if_ssh = smart
scp_extra_args = -T

[colors]
debug = bright gray
```

... and your `host.ini` should look something like this:
```ini
[dns_servers]
10.10.10.21
10.10.10.22

[vault_servers]
vault.cryogence.org

[swarm_servers]
swarm.cryogence.org
```

... and last but not least the `vault-secrets.yml` which loads all the secrets into our HC Vault server. Everything in ALL CAPS needs to be replaced with your own data.
```yml
# roles/dns_servers/vars/secrets.yml
# Secrets file. Encrypt with Ansible Vault!

local_user: MYUSERNAME

# List all credentials to load into Vault KV
vault_kv:
  adguard:
    username: MY-USERNAME
    password: MY-PASSWORD
TODO - need to finish this template...
```

# Adjust Variables File
Adjust any vars to match your environemnt. Make sure you choose an INTERNAL ONLY domain that you own

### Ansible Deployment
With all infrastructure deployed we are ready to configure the VMs.
1. Go ino your secrets directory: `cd /path/to/secrets/`
1. Copy the config file to you home directory: `cp ansible.cfg ~/`
1. Unlock all vault files: `ansible-vault decrypt --vault-password-file /path/to/secrets/ansible-key ~/homelab/ansible/{path/to/secrets.yml}`
1. Run the deployment playbook: `ansible-playbook /path-to/ansible/deploy.yml`
