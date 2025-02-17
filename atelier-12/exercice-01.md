# Exercice 01

Pour commencer nous écrivons le playbook chrony.yaml :

```
---
- name: Configure NTP with Chrony on Rocky Linux
  hosts: redhat
  become: yes
  tasks:
    - name: Install chrony package
      dnf:
        name: chrony
        state: present

    - name: Display default chrony.conf content
      command: cat /etc/chrony.conf
      register: chrony_conf_content

    - name: Show default chrony.conf
      debug:
        msg: "{{ chrony_conf_content.stdout_lines }}"

    - name: Deploy custom chrony.conf
      copy:
        content: |
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'

    - name: Restart and enable Chrony service
      service:
        name: chronyd
        state: restarted
        enabled: yes
```

Nous pouvons vérifier la syntax du playbook avec la commande suivante :

```
$ yamllint chrony.yaml

chrony.yaml
  4:11      warning  truthy value should be one of [false, true]  (truthy)
  39:18     warning  truthy value should be one of [false, true]  (truthy)
```

Nous obtenons des warnings mais pas d'erreurs. Nous pouvons alors éxecuter le
playbook :

```
$ ansible-playbook chrony.yaml

PLAY [Configure NTP with chrony] ****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Install chrony package] *******************************************************************************************************************************************************************************************************************************
ok: [target02]
ok: [target01]
changed: [target03]

TASK [Ensure chronyd service is enabled and started] ********************************************************************************************************************************************************************************************************
changed: [target02]
changed: [target01]
changed: [target03]

TASK [Backup original chrony configuration] *****************************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Configure chrony] *************************************************************************************************************************************************************************************************************************************
changed: [target03]
changed: [target02]
changed: [target01]

TASK [Restart chronyd service to apply new configuration] ***************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Afin de vérifier l'idempotence, on relance le playbook :

```
$ ansible-playbook chrony.yaml

PLAY [Configure NTP with chrony] ****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Install chrony package] *******************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Ensure chronyd service is enabled and started] ********************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target01]
ok: [target02]

TASK [Backup original chrony configuration] *****************************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

TASK [Configure chrony] *************************************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Restart chronyd service to apply new configuration] ***************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
