## Ansible Testing ##  


### Pre Tasks ###  

Installed molecule drivers for docker  
```  
pip3 install  molecule  
pip3 install yamllint  
pip3 install tox  
pip3 install molecule_docker molecule_podman  
pip3 install molecule[docker,lint]  
pip3 install molecule-plugins[docker]  

```  

Pulled aragast image  

### Task 1 ###  

\```  
$ molecule test -s ubuntu_xenial
CRITICAL 'molecule/ubuntu_xenial/molecule.yml' glob failed.  Exiting.
ERROR    'molecule/ubuntu_xenial/molecule.yml' glob failed.  Exiting.
```  

### Task 2 ###..

Created scenario default  
with molecule init scenario default  

### Task 3 ###  

1. Modified config files inside of molecule/default dir  
- molecule.yml - added platform instance fedora  
- converge.yml - added role vector   
- requirements.yml  

2. Created docker container fedora  
```  
docker run -itd --name fedora docker.io/pycontribs/fedora  
```   
3. Tried to test ansible role with molecule  
```  
$ molecule test
WARNING  Driver docker does not provide a schema.
WARNING  Driver docker does not provide a schema.
INFO     default ➜ discovery: scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     default ➜ prerun: Performing prerun with role_name_check=0...
...
TASK [vector : Enable vector service] ******************************************
fatal: [fedora]: FAILED! => {"changed": false, "msg": "Could not find the requested service vector: host"}

PLAY RECAP *********************************************************************
fedora                     : ok=7    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

CRITICAL Ansible return code was 2, command was: ansible-playbook --inventory /home/alex/.ansible/tmp/molecule.sNUc.default/inventory --skip-tags molecule-notest,notest --diff --force-handlers --inventory=test.ini /home/alex/Study/ansible/ansible-testing/ansible-roles/roles/vector/molecule/default/converge.yml
...
PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     default ➜ destroy: Executed: Successful
INFO     default ➜ scenario: Pruning extra files from scenario ephemeral directory
ERROR    Ansible return code was 2, command was: ansible-playbook --inventory /home/alex/.ansible/tmp/molecule.sNUc.default/inventory --skip-tags molecule-notest,notest --diff --force-handlers --inventory=test.ini /home/alex/Study/ansible/ansible-testing/ansible-roles/roles/vector/molecule/default/converge.yml
ERROR    Molecule executed 1 scenario (1 failed)

```  

Test installs package, creates config and systemd unit for vector from jinja templates, but fails because there is no systemd inside of pycontribs images.  
So, its too complex to fix it   
Looks like terraform test env + test ansible apply much easier.  

### Task 4 ###
I just can imagine how to work with asserts.  
For example:
```  


```  
