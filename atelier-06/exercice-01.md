# Exercice 01

Nous commençons par ajouter les entrées dans le fichier hosts :

```console
$ vim /etc/hosts

127.0.0.1      localhost.localdomain  localhost
192.168.56.10  control.sandbox.lan    control
192.168.56.20  target01.sandbox.lan   target01
192.168.56.30  target02.sandbox.lan   target02
192.168.56.40  target03.sandbox.lan   target03
```

Nous générons la paire de clé sur le noeud de control :

```console
$ ssh-keygen
```

Nous copions ensuite la clé publique sur les targets pour se connecter sans
mots de passe :  

```console
$ ssh-copy-id vagrant@target01
$ ssh-copy-id vagrant@target02
$ ssh-copy-id vagrant@target03
```

Nous testons la connexion :

```console
$ ssh vagrant@target01
$ ssh vagrant@target02
$ ssh vagrant@target03
```

Nous installons ansible avec les commandes :

```console
$ sudo apt update && sudo apt install -y ansible
```

Nous vérifions la version de ansible :

```console
$ ansible --version
ansible 2.10.8
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```

Nous testons la bonne configuration avec un ping sur les targets :

```console
$ ansible all -i target01,target02,target03 -u vagrant -m ping
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Nous créons un répertoire de travail :

```console
$ mkdir ~/monprojet
```

Nous créons un fichier de configuration ansible vide :

```console
$ touch ~/monprojet/ansible.cfg
```

Nous nous plaçons dans le dossier et avec la commande qui montre la version de
ansible, on constate que le fichier est bien pris en compte :

```console
~/monprojet$ ansible --version
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```

Nous ajoutons dedans les lignes suivantes :

```ini
[defaults]
inventory = ./hosts.ini
log_path = ~/journal/ansible.log
```

Nous créons le fichier hosts.ini :

```console
$ vim hosts.ini

[testlab]
target01
target02
target03
```

Nous créons le dossier qui va acceuillir le fichier de logs :

```console
$ mkdir -p ~/journal
```

Pour tester la journalisation, nous lançons le module de ping :

```console
$ ansible all -m ping
$ cat ~/journal/ansible.log

2025-02-12 10:59:59,595 p=3746 u=vagrant n=ansible | target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:59:59,597 p=3746 u=vagrant n=ansible | target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
2025-02-12 10:59:59,614 p=3746 u=vagrant n=ansible | target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Nous testons ensuite avec le groupe que nous avons créé dans le fichier
hosts.ini

```console
$ ansible testlab -m ping

target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Pour préciser le nom d'utilisateur à utiliser pour la connexion ssh, on ajoute
dans la config :

```ini
remote_user = vagrant
```

Nous retestons avec le module ping 

```console
$ ansible all -m ping

target03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
target02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Pour passer en sudo, il faut ajouter la ligne suivante dans la config :

```ini
become=True
```

Pour vérifier, on execute la commande whoami :

```console
$ ansible all -m command -a "whoami" --become

target03 | CHANGED | rc=0 >>
root
target02 | CHANGED | rc=0 >>
root
target01 | CHANGED | rc=0 >>
root
```

Pour afficher la premiere du fichier /etc/shadow :

```console
$ ansible all -m command -a "head -n 1 /etc/shadow" --become

target03 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target01 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
target02 | CHANGED | rc=0 >>
root:*:19769:0:99999:7:::
```

Pour finir, nous supprimons l'environnement vagrant :

```console
$ cd ~/formation-ansible/atelier-06
$ vagrant destroy -f
```
