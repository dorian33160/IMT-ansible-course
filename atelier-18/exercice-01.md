# Exercice 01

Dans un premier temps, nous allons écrire le template jinja2 pour la
configuration de chrony :

```
# {{ chrony_conf_path }}
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```

Dans un second temps, nous allons écrire le fichier de variables qui convient :

```yaml
---
# vars/chrony_vars.yml

chrony_package:
  Debian: chrony
  RedHat: chrony
  Suse: chrony

chrony_service:
  Debian: chrony
  RedHat: chronyd
  Suse: chronyd

chrony_conf_path:
  Debian: /etc/chrony/chrony.conf
  RedHat: /etc/chrony.conf
  Suse: /etc/chrony.conf
```

Pour finir, nous allons écrire le playbook qui se basera sur le
fichier de variables :

```yaml
---
# chrony.yml

- hosts: all
  become: true
  vars_files:
    - vars/chrony_vars.yml
  tasks:
    - name: Install Chrony
      package:
        name: "{{ chrony_package[ansible_os_family] }}"
        state: present

    - name: Start and enable Chrony service
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: started
        enabled: yes

    - name: Configure Chrony
      template:
        src: templates/chrony.conf.j2
        dest: "{{ chrony_conf_path[ansible_os_family] }}"
      notify: Restart Chrony

  handlers:
    - name: Restart Chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: restarted
```

Nous exécutons le playbook :

```console
$ ansible-playbook -i inventory playbooks/chrony.yml 

PLAY [all] **************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Install Chrony] ***************************************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
ok: [rocky]
changed: [suse]

TASK [Start and enable Chrony service] **********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
changed: [suse]

TASK [Configure Chrony] *************************************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
changed: [suse]
changed: [rocky]

RUNNING HANDLER [Restart Chrony] ****************************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
changed: [rocky]
changed: [suse]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=5    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=5    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous vérifions sur rocky la configuration :

```console
[vagrant@rocky ~]$ cat /etc/chrony.conf
# {'Debian': '/etc/chrony/chrony.conf', 'RedHat': '/etc/chrony.conf', 'Suse': '/etc/chrony.conf'}
server 0.fr.pool.ntp.org iburst
server 1.fr.pool.ntp.org iburst
server 2.fr.pool.ntp.org iburst
server 3.fr.pool.ntp.org iburst
driftfile /var/lib/chrony/drift
makestep 1.0 3
rtcsync
logdir /var/log/chrony
```
