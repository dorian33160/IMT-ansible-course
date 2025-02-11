# Exercice 03

Comme précisé, il n'est pas nécéssaire d'installer python sur Rocky. Nous
pouvons directement passer à la suite et créer l'environnemnet Venv :  

```
python3 -m venv ~/.venv/ansible
```

Nous pouvons ensuite lancer le Venv :  

```
source ~/.venv/ansible/bin/activate
```
Nous mettons alors à jour pip :  

```
(ansible) [vagrant@rocky ~]$ pip install --upgrade pip
```

On install avec pip ansible :  

```
pip install ansible
```

Il ne reste plus qu'à vérifier la version de ansible :

```
ansible --version
ansible [core 2.15.13]
  config file = None
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /home/vagrant/.venv/ansible/lib64/python3.9/site-packages/ansible
  ansible collection location = /home/vagrant/.ansible/collections:/usr/share/ansible/collections
  executable location = /home/vagrant/.venv/ansible/bin/ansible
  python version = 3.9.18 (main, Sep  7 2023, 00:00:00) [GCC 11.4.1 20230605 (Red Hat 11.4.1-2)] (/home/vagrant/.venv/ansible/bin/python3)
  jinja version = 3.1.5
  libyaml = True
```
