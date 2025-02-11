# Exercice 01  

Nous commençons par mettre à jour le cache APT :  

```
$ sudo apt update
```
Nous cherchons ensuite le nom du paquet :  

```
$ sudo apt-cache search --names-only ansible
```

Nous installons le paquet :  

```
$ sudo apt install ansible
```

Pour finir, nous vérifions la version de ansible :  

```
$ ansible --version
```

On obtient :  

```
ansible 2.10.8
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
```
