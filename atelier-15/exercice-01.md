# Exercice 01

Nous commençons par écrir le playbook qui affiche les infos détaillées du noyau
avec le module debug et msg :

```
# kernel.yml
---
- name: Afficher les informations détaillées du noyau
  hosts: all
  tasks:
    - name: Obtenir les informations du noyau
      command: uname -a
      register: kernel_info

    - name: Afficher les informations du noyau avec msg
      debug:
        msg: "{{ kernel_info.stdout }}"
```

Voici le retour de l'exécution du playbook :

```
$ ansible-playbook -i inventory playbooks/kernel-v1.yml 

PLAY [Afficher les informations détaillées du noyau] ********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Obtenir les informations du noyau] ********************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [rocky]
changed: [suse]

TASK [Afficher les informations du noyau avec msg] **********************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
}
ok: [debian] => {
    "msg": "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
}
ok: [suse] => {
    "msg": "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous modifions le playbook pour qu'il utilise debug et var :

```
# kernel.yml
---
- name: Afficher les informations détaillées du noyau
  hosts: all
  tasks:
    - name: Obtenir les informations du noyau
      command: uname -a
      register: kernel_info

    - name: Afficher les informations du noyau avec var
      debug:
        var: kernel_info.stdout
```

Voici le retour de l'exécution du playbook :

```console
$ ansible-playbook -i inventory playbooks/kernel-v2.yml 

PLAY [Afficher les informations détaillées du noyau] ********************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [rocky]
ok: [suse]

TASK [Obtenir les informations du noyau] ********************************************************************************************************************************************************************************************************************
changed: [debian]
changed: [suse]
changed: [rocky]

TASK [Afficher les informations du noyau avec var] **********************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "kernel_info.stdout": "Linux rocky 5.14.0-362.13.1.el9_3.x86_64 #1 SMP PREEMPT_DYNAMIC Wed Dec 13 14:07:45 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux"
}
ok: [debian] => {
    "kernel_info.stdout": "Linux debian 6.1.0-17-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.69-1 (2023-12-30) x86_64 GNU/Linux"
}
ok: [suse] => {
    "kernel_info.stdout": "Linux suse 5.14.21-150500.55.39-default #1 SMP PREEMPT_DYNAMIC Tue Dec 5 10:06:35 UTC 2023 (2e4092e) x86_64 x86_64 x86_64 GNU/Linux"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
rocky                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Pour finir, nous écrivons le playbook qui affiche le nombre total de paquets RPM installés sur les hôtes rocky et suse :

```
# packages.yml
---
- name: Afficher le nombre total de paquets RPM installés
  hosts: rocky, suse
  tasks:
    - name: Obtenir le nombre total de paquets RPM installés
      shell: rpm -qa | wc -l
      register: rpm_count

    - name: Afficher le nombre total de paquets RPM installés
      debug:
        msg: "Nombre total de paquets RPM installés : {{ rpm_count.stdout }}"
```

Voici le retour de l'exécution du playbook :

```console
$ ansible-playbook -i inventory playbooks/packages.yml 

PLAY [Afficher le nombre total de paquets RPM installés] ****************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [rocky]
ok: [suse]

TASK [Obtenir le nombre total de paquets RPM installés] *****************************************************************************************************************************************************************************************************
changed: [rocky]
changed: [suse]

TASK [Afficher le nombre total de paquets RPM installés] ****************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Nombre total de paquets RPM installés : 671"
}
ok: [suse] => {
    "msg": "Nombre total de paquets RPM installés : 917"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
rocky                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
suse                       : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
