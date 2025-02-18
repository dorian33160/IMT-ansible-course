# Exercice 02

Nous commençons par ajouter le répository officiel de ansible :  

```console
$ sudo apt-add-repository ppa:ansible/ansible
```

Il faut alors mettre à jour le cache :

```console
$ sudo apt update
```

Nous installons ensuite le paquet :  

```console
$ sudo apt install ansible
```

Nous vérifions la version de ansible :  

```
ansible [core 2.17.8]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0] (/usr/bin/python3)
  jinja version = 3.0.3
  libyaml = True
```
