# Exercice 01

Nous commençons par écrire le premier playbook :

```
# myvars1.yml
---
- name: Afficher voiture et moto préférées
  hosts: all
  vars:
    mycar: "Toyota"
    mybike: "Yamaha"
  tasks:
    - name: Afficher la voiture préférée
      debug:
        msg: "Ma voiture préférée est {{ mycar }}"

    - name: Afficher la moto préférée
      debug:
        msg: "Ma moto préférée est {{ mybike }}"
```

Pour l'exécuter, nous lançons la commande :

```
$ ansible-playbook -i inventory playbooks/myvars1.yml

PLAY [Afficher voiture et moto préférées] *******************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Afficher la voiture préférée] *************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma voiture préférée est Toyota"
}
ok: [target02] => {
    "msg": "Ma voiture préférée est Toyota"
}
ok: [target03] => {
    "msg": "Ma voiture préférée est Toyota"
}

TASK [Afficher la moto préférée] ****************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma moto préférée est Yamaha"
}
ok: [target02] => {
    "msg": "Ma moto préférée est Yamaha"
}
ok: [target03] => {
    "msg": "Ma moto préférée est Yamaha"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target02                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
target03                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Nous testons ensuite avec l'override de variables :

```
$ ansible-playbook -i inventory playbooks/myvars1.yml --extra-vars "mycar=Ferrari mybike=Dcuati"

PLAY [Afficher voiture et moto préférées] *******************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Afficher la voiture préférée] *************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma voiture préférée est Ferrari"
}
ok: [target02] => {
    "msg": "Ma voiture préférée est Ferrari"
}
ok: [target03] => {
    "msg": "Ma voiture préférée est Ferrari"
}

TASK [Afficher la moto préférée] ****************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma moto préférée est Ducati"
}
ok: [target02] => {
    "msg": "Ma moto préférée est Ducati"
}
ok: [target03] => {
    "msg": "Ma moto préférée est Ducati"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous écrivons le deuxieme playbook :

```
$ ansible-playbook -i inventory playbooks/myvars2.yml

PLAY [Afficher voiture et moto préférées avec set_fact] *****************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target03]
ok: [target02]
ok: [target01]

TASK [Définir les variables mycar et mybike] ****************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Afficher la voiture préférée] *************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma voiture préférée est Toyota"
}
ok: [target02] => {
    "msg": "Ma voiture préférée est Toyota"
}
ok: [target03] => {
    "msg": "Ma voiture préférée est Toyota"
}

TASK [Afficher la moto préférée] ****************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma moto préférée est Yamaha"
}
ok: [target02] => {
    "msg": "Ma moto préférée est Yamaha"
}
ok: [target03] => {
    "msg": "Ma moto préférée est Yamaha"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Avec l'override des variables :

```
$ ansible-playbook -i inventory playbooks/myvars2.yml --extra-vars "mycar=Ferrari mybike=Ducati"

PLAY [Afficher voiture et moto préférées avec set_fact] *****************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target02]
ok: [target03]
ok: [target01]

TASK [Définir les variables mycar et mybike] ****************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target02]
ok: [target03]

TASK [Afficher la voiture préférée] *************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma voiture préférée est Ferrari"
}
ok: [target02] => {
    "msg": "Ma voiture préférée est Ferrari"
}
ok: [target03] => {
    "msg": "Ma voiture préférée est Ferrari"
}

TASK [Afficher la moto préférée] ****************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma moto préférée est Ducati"
}
ok: [target02] => {
    "msg": "Ma moto préférée est Ducati"
}
ok: [target03] => {
    "msg": "Ma moto préférée est Ducati"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous écrivons le troisième playbook :

```
# myvars3.yml
---
- name: Afficher voiture et moto préférées sans les définir
  hosts: all
  tasks:
    - name: Afficher la voiture préférée
      debug:
        msg: "Ma voiture préférée est {{ mycar }}"

    - name: Afficher la moto préférée
      debug:
        msg: "Ma moto préférée est {{ mybike }}"
```

Nous devons également ajouter les variables dans le inventory :

```
[all:vars]
mycar=VW
mybike=BMW
```

Nous exécutons le playbook avec la commande :

```
$ ansible-playbook -i inventory playbooks/myvars3.yml

PLAY [Afficher voiture et moto préférées sans les définir] **************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target02]
ok: [target01]
ok: [target03]

TASK [Afficher la voiture préférée] *************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma voiture préférée est VW"
}
ok: [target02] => {
    "msg": "Ma voiture préférée est VW"
}
ok: [target03] => {
    "msg": "Ma voiture préférée est VW"
}

TASK [Afficher la moto préférée] ****************************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Ma moto préférée est BMW"
}
ok: [target02] => {
    "msg": "Ma moto préférée est BMW"
}
ok: [target03] => {
    "msg": "Ma moto préférée est BMW"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Nous écrivons le dernier playbook :

```
# display_user.yml
---
- name: Afficher utilisateur et mot de passe
  hosts: all
  vars_prompt:
    - name: "user"
      prompt: "Entrez le nom d'utilisateur"
      default: "microlinux"
      private: no

    - name: "password"
      prompt: "Entrez le mot de passe"
      default: "yatahongaga"
      private: yes

  tasks:
    - name: Afficher l'utilisateur et le mot de passe
      debug:
        msg: "Utilisateur: {{ user }}, Mot de passe: {{ password }}"
```

Nous l'exécutons avec la commande :

```
$ ansible-playbook -i inventory playbooks/display_user.yml

Entrez le nom d'utilisateur [microlinux]: test
Entrez le mot de passe [yatahongaga]:

PLAY [Afficher utilisateur et mot de passe] *****************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [target01]
ok: [target03]
ok: [target02]

TASK [Afficher l'utilisateur et le mot de passe] ************************************************************************************************************************************************************************************************************
ok: [target01] => {
    "msg": "Utilisateur: test, Mot de passe: test"
}
ok: [target02] => {
    "msg": "Utilisateur: test, Mot de passe: test"
}
ok: [target03] => {
    "msg": "Utilisateur: test, Mot de passe: test"
}

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
target01                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target02                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
target03                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
