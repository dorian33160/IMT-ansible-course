# Exercice 01

Nous commençons par écrire le premier playbook qui va installer chrony en
fonction de la distribution de la machine avec une variable globale :

```yaml
---
# chrony-01.yml

- hosts: all
  become: true
  tasks:
    - name: Install Chrony on Debian/Ubuntu
      apt:
        name: chrony
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Install Chrony on Rocky Linux
      dnf:
        name: chrony
        state: present
      when: ansible_os_family == "RedHat"

    - name: Install Chrony on SUSE Linux
      zypper:
        name: chrony
        state: present
      when: ansible_os_family == "Suse"

    - name: Start and enable Chrony service
      service:
        name: "{{ 'chrony' if ansible_os_family == 'Debian' else 'chronyd' }}"
        state: started
        enabled: yes

    - name: Configure Chrony on Debian/Ubuntu
      copy:
        dest: /etc/chrony/chrony.conf
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart Chrony
      when: ansible_os_family == "Debian"

    - name: Configure Chrony on Rocky Linux
      copy:
        dest: /etc/chrony.conf
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart Chrony
      when: ansible_os_family == "RedHat"

    - name: Configure Chrony on SUSE Linux
      copy:
        dest: /etc/chrony.conf
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart Chrony
      when: ansible_os_family == "Suse"

  handlers:
    - name: Restart Chrony
      service:
        name: "{{ 'chrony' if ansible_os_family == 'Debian' else 'chronyd' }}"
        state: restarted
```

Nous exécutons le playbook :

```console
$ ansible-playbook -i inventory playbooks/chrony-01.yml 

PLAY [all] **************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [ubuntu]
ok: [suse]

TASK [Install Chrony on Debian/Ubuntu] **********************************************************************************************************************************************************************************************************************
skipping: [rocky]
skipping: [suse]
changed: [debian]
changed: [ubuntu]

TASK [Install Chrony on Rocky Linux] ************************************************************************************************************************************************************************************************************************
skipping: [debian]
skipping: [suse]
skipping: [ubuntu]
ok: [rocky]

TASK [Install Chrony on SUSE Linux] *************************************************************************************************************************************************************************************************************************
skipping: [rocky]
skipping: [debian]
skipping: [ubuntu]
changed: [suse]

TASK [Start and enable Chrony service] **********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [rocky]
changed: [suse]

TASK [Configure Chrony on Debian/Ubuntu] *************************************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [ubuntu]
skipping: [rocky]
skipping: [suse]

TASK [Configure Chrony on Rocky Linux] **********************************************************************************************************************************************************************************************************************
skipping: [debian]
skipping: [suse]
skipping: [ubuntu]
changed: [rocky]

TASK [Configure Chrony on SUSE Linux] ***********************************************************************************************************************************************************************************************************************
skipping: [rocky]
skipping: [debian]
skipping: [ubuntu]
changed: [suse]

RUNNING HANDLER [Restart Chrony] ****************************************************************************************************************************************************************************************************************************
changed: [rocky]
changed: [suse]
changed: [debian]
changed: [ubuntu]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=4    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
rocky                      : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
suse                       : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
ubuntu                     : ok=4    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
```

Nous écrivons le second playbook qui utiliseras un fichier de variables :

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

chrony_confdir:
  Debian: /etc/chrony
  RedHat: /etc
  Suse: /etc
```

```yaml
---
# chrony-02.yml

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
      copy:
        dest: "{{ chrony_confdir[ansible_os_family] }}/chrony.conf"
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart Chrony

  handlers:
    - name: Restart Chrony
      service:
        name: "{{ chrony_service[ansible_os_family] }}"
        state: restarted
```

Nous pouvons exécuter le playbook :

```console
$ ansible-playbook -i inventory playbooks/chrony-02.yml

PLAY [all] **************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]
ok: [ubuntu]

TASK [Install Chrony] ***************************************************************************************************************************************************************************************************************************************
ok: [suse]
ok: [debian]
ok: [ubuntu]
ok: [rocky]

TASK [Start and enable Chrony service] **********************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [ubuntu]
ok: [suse]
ok: [rocky]

TASK [Configure Chrony] *************************************************************************************************************************************************************************************************************************************
ok: [ubuntu]
ok: [debian]
ok: [suse]
ok: [rocky]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
