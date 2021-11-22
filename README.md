# TP Ansible1 - Communication Ynov DevOps B3


## **Rendu :** Un fichier MD : DEVOPS_ANSIBLE1_[NOM]\_[PRENOM].md

Vous avez un template de rendu dans le repo. 
Pour chaque étape, documenter vos actions : 

        Screenshot commande + output
        Explication d'une ou 2 ligne sur ce que fait la commande
        
A chaque exercice rajouter un titre et le nom de l'exercice. La syntaxe ainsi que l'upload de l'image sont décrit dans des liens en haut du template.

:bangbang::bangbang: Pensez à renommer le fichier avec votre **Nom** et **Prénom**

:sparkles: Une fois le TP et le rendu terminé commitez et pushez le dans le repo. :sparkles:
  
### Tips   
Si vous avez des problèmes sur une command utilisez `ansible --help` et surtout aller voir la **documentation**. Elle est extremement exhaustive. les app

:raising_hand: Si vous avez des soucis n'hésitez pas à m'appeler. 
 
## Exercice 1: Communication

- Créer 3 machines virtuelles Linux dans le même subnet.
  - un node manager (Vous pouvez réutiliser votre machine Linux de Docker)
  - deux host
  - **Mettez le même mot de passe root pour les 3 machines**
- Autoriser la connexion **PubkeyAuthentication** depuis le node manager vers les hosts.
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





