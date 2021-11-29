# TP Ansible1 - Communication Ynov DevOps B3
 
## Exercice 1: Communication

- Créer 3 machines virtuelles Linux dans le même subnet.
  - un node manager (Vous pouvez réutiliser votre machine Linux de Docker)
  - deux host
  - **Mettez le même mot de passe root pour les 3 machines**
- Autoriser la connexion **PubkeyAuthentication** depuis le node manager vers les hosts.


Pour cet exercice, j'ai utilisé des containers pluôt que des VMs.
Cela a un avantage de rapidité, cependant la gestion des machines est différentes certaines programmes ne fonctionnent pas par défaut. Cela rajoute des étapes de debug. 


Voici le **Dockerfile** utilisé : 



- Créer votre inventaire Ansible
  - User par défaut : `root`
- Lancer un ping pour vérifier la communication via Ansible (Ad-hoc ou playbook).

## Exercice 2 : Préparation de l'environnement via Ansible

- Creer l’utilisateur user-ansible sur les nodes
  - Module user (Adhoc ou Playbook : https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html )
- Donner les droits sudo à user-ansible avec le module user
- Creer et pousser la clef publique de `user_ansible` pour pouvoir lancer les actions via cet utilisateur
  - Module authorized_keys : https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html
- Lancer une commande en sudo (*become*) avec l'utilisateur `user_ansible` : id ou whoami par exemple. 





