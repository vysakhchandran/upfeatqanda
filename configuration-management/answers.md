### Configuration Management (CM)

1. For managing the server above using your proposed configuration/settings, we don't want to do this manually. Assume
   we need to configure 10+ servers in pretty much the same way, all of which will be running kubernetes, either in the
   same cluster or different clusters. Using the tool(s) of your choice (ansible, puppet, chef, etc), provide an example
   of how you would achieve securing the servers and applying configuration using CM. Include any sample scripts or code
   snippets (any yaml's, templates, manifests, etc), as well as details on how you would manage any secrets (ie. if you
   require any root passwords in order to run your CM scripts). Try to provide as much relevant code as possible while
   being detailed in your answer. You do not need to write a fully working example; we are mostly interested in your
   thought process and how you would structure and manage the configuration of servers as well as evaluate your
   proficiency in using the tool(s) of your choice.

Answer
----------
The highlevel steps for the installation is pretty straight forward and [here is the fully working git repo](https://github.com/buildvirtual-git/kubernetes/tree/main/ansible-deploy-k8s) of an automated ansible K8 cluster deployment ( as this contains all working code am not cherry-picking and snipping it in here ) Installing the control-plane on bare metal manually and managing it is a time intensive task when compare with other solutions (AWS EKS cluster takes around 10-20 min to spinup). There might be problems with repos/libraries being obsolete that need to be handled on the fly. However in overall this method will do the magic. 

As our CM tool in choice is ansible.we can make use of privileged escalation to run a CM script as root on the target server. 
```
on ansible.cfg file

[defaults]
Inventory=inventory
		remote_user=<username>
roles_path= /home/admin/ansible/roles

[privilege_escalations]
become=True
become_method=sudo
become_user=root
become_ask_pass=False

```

And there are multiple ways we can handle secretes in Ansible. One common method is to use ansible vault as follows 

Step 1: create an encrypted vault with secret
```
# Plaintext YAML file
$ cat secrets_file.enc
api_key: SuperSecretPassword

# Encrypt the file with ansible-vault
$ ansible-vault encrypt secrets_file.enc
New Vault password:
Confirm New Vault password:
Encryption successful

# Confirm that the file now contains encrypted content
$ cat secrets_file.enc
$ANSIBLE_VAULT;1.1;AES256
38396162626134393935663839666463306231653861336630613938303662633538633836656465
3637353766613339663032363538626430316135623665340a653961303730353962386134393162
62343936366265353935346336643865643833353737613962643539373230616239346133653464
6435353361373263640a376632613336366430663761363339333737386637383961363833303830
3433653562373631303131316235383166613934366265366536613463383264666
```

Step 2: Refering the secret from playbook
```
$ cat main.yml
---

- hosts: all
  gather_facts: false
  tasks:
    - name: Ensure API key is present in config file
      ansible.builtin.lineinfile:
        path: /etc/app/configuration.ini
        line: "API_KEY={{ api_key }}"
```

Step 3: Executing the playbook 
```

In Automated execution save the vault password in a text file localy and pass it as a commandline argument 
ansible-playbook create_user.yml  --vault-password-file password.txt       

Here is the step for password prompt (ideal for manual executions)
$ ansible-playbook -i inventory.ini -e @secrets_file.enc --ask-vault-pass main.yml
Vault password:

PLAY [all] ***********************************************************************************

TASK [Ensure API key is present in config file] **********************************************
changed: [localhost]

PLAY RECAP ***********************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

$ cat /etc/app/configuration.ini
API_KEY=SuperSecretPassword

The other way is to make use of a centralisd password manager like hashicorp vault. If we are pushing this environment to cloud i would prefer to make use of the service providers valut like AWS Secret manager or microsoft key vault which is always available and have nominal pricing. 


