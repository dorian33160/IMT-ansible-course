# Exercice 01

Nous commençons par écrire les 3 playbooks.

Voici le premier pour debian :

```yaml
---
- name: Install Apache on Debian
  hosts: debian
  become: yes
  tasks:
    - name: Install Apache package
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Deploy custom index.html
      copy:
        content: "Apache web server running on Debian Linux"
        dest: /var/www/html/index.html

    - name: Start and enable Apache service
      service:
        name: apache2
        state: started
        enabled: yes
```

Voici le deuxième pour rocky :

```yaml
---
- name: Install Apache on Rocky Linux
  hosts: rocky
  become: yes
  tasks:
    - name: Install Apache package
      dnf:
        name: httpd
        state: present

    - name: Deploy custom index.html
      copy:
        content: "Apache web server running on Rocky Linux"
        dest: /var/www/html/index.html

    - name: Start and enable Apache service
      service:
        name: httpd
        state: started
        enabled: yes
```

Voici le dernier pour suse :

```yaml
---
- name: Install Apache on SUSE
  hosts: suse
  become: yes
  tasks:
    - name: Install Apache package
      zypper:
        name: apache2
        state: present

    - name: Deploy custom index.html
      copy:
        content: "Apache web server running on SUSE Linux"
        dest: /srv/www/htdocs/index.html

    - name: Start and enable Apache service
      service:
        name: apache2
        state: started
        enabled: yes
```

On execute ensuite chaque playbook avec la commande suivante :

```console
$ ansible-playbook apache-debian.yml
$ ansible-playbook apache-rocky.yml
$ ansible-playbook apache-suse.yml
```

On obtient les résultats suivant :

Pour debian :

```console
PLAY [Install Apache on Debian] *****************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [debian]

TASK [Install Apache package] *******************************************************************************************************************************************************************************************************************************
changed: [debian]

TASK [Deploy custom index.html] *****************************************************************************************************************************************************************************************************************************
changed: [debian]

TASK [Start and enable Apache service] **********************************************************************************************************************************************************************************************************************
ok: [debian]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
debian                     : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

$ curl http://debian

Apache web server running on Debian Linux
```

Pour rocky :

```console
PLAY [Install Apache on Rocky Linux] ************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [rocky]

TASK [Install Apache package] *******************************************************************************************************************************************************************************************************************************
changed: [rocky]

TASK [Deploy custom index.html] *****************************************************************************************************************************************************************************************************************************
changed: [rocky]

TASK [Start and enable Apache service] **********************************************************************************************************************************************************************************************************************
changed: [rocky]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
rocky                      : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

$ curl http://rocky

Apache web server running on Rocky Linux
```

Pour suse :

```console
PLAY [Install Apache on SUSE] *******************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] **************************************************************************************************************************************************************************************************************************************
ok: [suse]

TASK [Install Apache package] *******************************************************************************************************************************************************************************************************************************
changed: [suse]

TASK [Deploy custom index.html] *****************************************************************************************************************************************************************************************************************************
changed: [suse]

TASK [Start and enable Apache service] **********************************************************************************************************************************************************************************************************************
changed: [suse]

PLAY RECAP **************************************************************************************************************************************************************************************************************************************************
suse                       : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

$ curl http://suse

Apache web server running on SUSE Linux
```
