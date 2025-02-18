# Exercice 01

Nous commençons par écrire le playbook qui affiche le gestionnaire de paquets
de chaque machine :

```yaml
---
- name: Afficher le gestionnaire de paquets
  hosts: all
  tasks:
    - name: Afficher le gestionnaire de paquets
      debug:
        msg: "Le gestionnaire de paquets est {{ ansible_pkg_mgr }}"
```

Nous exécutons ensuite la playbook :

```console
$ ansible-playbook -i inventory playbooks/pkg-info.yml

PLAY [Afficher le gestionnaire de paquets] ******************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Afficher le gestionnaire de paquets] ******************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Le gestionnaire de paquets est dnf"
}
ok: [debian] => {
    "msg": "Le gestionnaire de paquets est apt"
}
ok: [suse] => {
    "msg": "Le gestionnaire de paquets est zypper"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous écrivons ensuite le deuxieme playbook qui permet de récupérer la version
de python de chaque machine :

```yaml
---
- name: Afficher la version de Python
  hosts: all
  tasks:
    - name: Afficher la version de Python
      debug:
        msg: "La version de Python est {{ ansible_python_version }}"
```

Nous exécutons ensuite la playbook :

```console
$ ansible-playbook -i inventory playbooks/python-info.yml

PLAY [Afficher la version de Python] ************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Afficher la version de Python] ************************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "La version de Python est 3.9.18"
}
ok: [debian] => {
    "msg": "La version de Python est 3.11.2"
}
ok: [suse] => {
    "msg": "La version de Python est 3.6.15"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Pour finir, nous écrivons le dernier playbook qui permet de récupérer les
serveurs DNS utilisés pour chaque machine :

```yaml
---
- name: Afficher les serveurs DNS
  hosts: all
  tasks:
    - name: Afficher les serveurs DNS
      debug:
        msg: "Les serveurs DNS sont {{ ansible_dns.nameservers }}"
```

Nous exécutons ensuite la playbook :

```console
$ ansible-playbook -i inventory playbooks/dns-info.yml

PLAY [Afficher les serveurs DNS] ****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]
ok: [suse]
ok: [rocky]

TASK [Afficher les serveurs DNS] ****************************************************************************************************************************************************************************************************************************
ok: [rocky] => {
    "msg": "Les serveurs DNS sont ['10.0.2.3']"
}
ok: [debian] => {
    "msg": "Les serveurs DNS sont ['4.2.2.1', '4.2.2.2', '208.67.220.220']"
}
ok: [suse] => {
    "msg": "Les serveurs DNS sont ['10.0.2.3']"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
rocky                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
suse                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
