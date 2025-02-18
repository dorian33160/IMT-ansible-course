# Exercice 01

Pour commencer nous écrivons le playbook chrony.yaml :

```yaml
---
- name: Configure NTP with chrony
  hosts: redhat
  become: yes
  tasks:
    - name: Install chrony package
      ansible.builtin.yum:
        name: chrony
        state: present

    - name: Ensure chronyd service is enabled and started
      ansible.builtin.service:
        name: chronyd
        enabled: yes
        state: started

    - name: Backup original chrony configuration
      ansible.builtin.copy:
        src: /etc/chrony.conf
        dest: /etc/chrony.conf.bak
        remote_src: yes
        backup: yes

    - name: Configure chrony
      ansible.builtin.copy:
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
      notify: Restart chronyd

  handlers:
    - name: Restart chronyd
      ansible.builtin.service:
        name: chronyd
        state: restarted

```

Nous pouvons vérifier la syntax du playbook avec la commande suivante :

```console
$ yamllint chrony.yaml

chrony.yaml
  4:11      warning  truthy value should be one of [false, true]  (truthy)
  39:18     warning  truthy value should be one of [false, true]  (truthy)
```

Nous obtenons des warnings mais pas d'erreurs. Nous pouvons alors éxecuter le
playbook :

```
PLAY [Configure NTP with chrony] ****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Install chrony package] *******************************************************************************************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Ensure chronyd service is enabled and started] ********************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Backup original chrony configuration] *****************************************************************************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Configure chrony] *************************************************************************************************************************************************************************************************************************************
changed: [target03]
changed: [target01]
changed: [target02]

RUNNING HANDLER [Restart chronyd] ***************************************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target02]
changed: [target03]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=6    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Afin de vérifier l'idempotence, on relance le playbook :

```console
$ ansible-playbook chrony.yaml

PLAY [Configure NTP with chrony] ****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Install chrony package] *******************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Ensure chronyd service is enabled and started] ********************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Backup original chrony configuration] *****************************************************************************************************************************************************************************************************************
changed: [target01]
changed: [target03]
changed: [target02]

TASK [Configure chrony] *************************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous constatons qu'il n'y a pas eu de redémarrage du service car nous n'avons
pas modifié la configuration entre temps 
