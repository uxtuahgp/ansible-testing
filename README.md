## Ansible Testing ##  


## Pre Tasks ##  

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

## Molecule ##  

### Task 1 ###  

```  
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
Like this:  
```  
  tasks:
    - name: Vector config file stat
      ansible.builtin.stat:
        path: /etc/vector/vector.yml
      register: vector_config
    - name: Assert something
      ansible.builtin.assert:
        that: vector_config.stat
```  

### Task 5 ###  

### Task 6 ###  
Added tag  
Pushed repo  

## Tox ##  

### Task 1 ###  
Added example files  

### Task 2 ###  
Got shell inside of tox containeer  
```  
$ docker run --privileged=True -v ~/Study/ansible/ansible-testing/ansible-roles/roles/vector:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash
[root@d5e2bf80014d vector-role]#
```  

### Task 3 ###  
Got a long output  
```  
[root@d5e2bf80014d vector-role]# tox
py37-ansible210 create: /opt/vector-role/.tox/py37-ansible210
py37-ansible210 installdeps: -rtox-requirements.txt, ansible<3.0
...
py39-ansible30 run-test-pre: PYTHONHASHSEED='2656447985'
py39-ansible30 run-test: commands[0] | molecule test -s compatibility --destroy always
CRITICAL 'molecule/compatibility/molecule.yml' glob failed.  Exiting.
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible30/bin/molecule test -s compatibility --destroy always (exited with code 1)
_________________________________________________________________________________________________ summary __________________________________________________________________________________________________
ERROR:   py37-ansible210: commands failed
ERROR:   py37-ansible30: commands failed
ERROR:   py39-ansible210: commands failed
ERROR:   py39-ansible30: commands failed
```  

### Task 4 ###  

Created and teted podman scenario with just create, clean and destroy  
```  
$ molecule test -s podman
WARNING  Driver podman does not provide a schema.
WARNING  default ➜ config: The scenario config file ('/home/alex/Study/ansible/ansible-testing/ansible-roles/roles/vector/molecule/default/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
WARNING  Driver docker does not provide a schema.
INFO     podman ➜ discovery: scenario test matrix: dependency, cleanup, destroy
INFO     podman ➜ prerun: Performing prerun with role_name_check=0...
...
PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     podman ➜ destroy: Executed: Successful
INFO     podman ➜ scenario: Pruning extra files from scenario ephemeral directory
WARNING  Molecule executed 1 scenario (1 missing files)
```  
### Task 5 ###  
Changed scenario name in tox.ini  
```  
commands =
    {posargs:molecule test -s podman --destroy always}

```  
### Task 6 ###  
```  
[root@62dcac199381 vector-role]# tox
py37-ansible210 installed: ansible==2.10.7,ansible-base==2.10.17,ansible-compat==1.0.0,arrow==1.2.3,bcrypt==4.2.1,binaryornot==0.4.4,cached-property==1.5.2,Cerberus==1.3.8,certifi==2026.2.25,cffi==1.15.1,chardet==5.2.0,charset-normalizer==3.4.4,click==8.1.8,click-help-colors==0.9.4,cookiecutter==2.6.0,cryptography==45.0.7,distro==1.9.0,enrich==1.2.7,idna==3.10,importlib-metadata==6.7.0,Jinja2==3.1.6,jmespath==1.0.1,lxml==5.4.0,markdown-it-py==2.2.0,MarkupSafe==2.1.5,mdurl==0.1.2,molecule==3.6.1,molecule-podman==1.1.0,packaging==24.0,paramiko==2.12.0,pluggy==1.2.0,pycparser==2.21,Pygments==2.17.2,PyNaCl==1.5.0,python-dateutil==2.9.0.post0,python-slugify==8.0.4,PyYAML==6.0.1,requests==2.31.0,rich==13.8.1,selinux==0.2.1,six==1.17.0,subprocess-tee==0.3.5,text-unidecode==1.3,typing_extensions==4.7.1,urllib3==2.0.7,zipp==3.15.0
...
py37-ansible210 run-test-pre: PYTHONHASHSEED='3489143964'
py37-ansible210 run-test: commands[0] | molecule test -s podman --destroy always
/opt/vector-role/.tox/py37-ansible210/lib/python3.7/site-packages/ansible/parsing/vault/__init__.py:44: CryptographyDeprecationWarning: Python 3.7 is no longer supported by the Python core team and support for it is deprecated in cryptography. The next release of cryptography will remove support for Python 3.7.
  from cryptography.exceptions import InvalidSignature
INFO     podman scenario test matrix: dependency, cleanup, destroy
...
py39-ansible30 run-test: commands[0] | molecule test -s podman --destroy always
WARNING  Driver podman does not provide a schema.
CRITICAL Failed to validate /opt/vector-role/molecule/podman/molecule.yml

["Additional properties are not allowed ('ansible' was unexpected)"]
ERROR: InvocationError for command /opt/vector-role/.tox/py39-ansible30/bin/molecule test -s podman --destroy always (exited with code 1)
_________________________________________________________________________________________________ summary __________________________________________________________________________________________________
ERROR:   py37-ansible210: commands failed
ERROR:   py37-ansible30: commands failed
ERROR:   py39-ansible210: commands failed
ERROR:   py39-ansible30: commands failed
```  
Some stuff happened
