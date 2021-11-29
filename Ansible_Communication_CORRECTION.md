# CORRECTION
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

![image](https://user-images.githubusercontent.com/51991304/143885216-3ff08603-5e42-41d8-b72f-033ba8f85881.png)


Dans ce Dockerfile, j'utilise l'image de base Ubuntu. Cela me permet d'avoir une base Linux contenant bash et me facilite le travail. 

J'y installe SSH qui est **obligatoire** pour la communication entre Ansible et les nodes. 

Entre les lignes 6 et 8, je modifie la configuration de ssh pour y autoriser les connexions avec clef publique ainsi que les clefs RSA. 

La commande *sed* permet de parser un fichier et d'y effectuer des modifications en utilisant des Regex et un module de recherche `s/pattern_to_replace/new_pattern/g`

Pour gérer l'image je créer un utilisateur **test** sur le container et je lui donne le groupe **sudo** pour la suite du TP. 

Je start ensuite le service SSH dans l'instruction **CMD** pour que le service soit up lors du lancement du container. 

![image](https://user-images.githubusercontent.com/51991304/143883765-ba28bb9a-cdae-42c1-8348-49adb3855e2c.png)

Je lance 2 containers SSH pour pouvoir continuer le TP. 

Pour faciliter la gestion de mon fichier inventaire, je donne des hostnames précis à mes containers, pour cela je rajoute des lignes dans le fichier `/etc/hosts` qui permet de lier une IP à un nom de domaine. 

![image](https://user-images.githubusercontent.com/51991304/143883906-3be72bff-bb13-4b09-a479-05db14e81f61.png)



L'inventaire Ansible est le fichier catalogue qui regroupe les machines sur lesquelles Ansible va appliquer des configurations ou jouer des playbook. 

Le fichier inventaire est écrit soit en yml soit en ini. 

![image](https://user-images.githubusercontent.com/51991304/143884532-c3173674-af0e-48da-9ae7-25ada9ede1ce.png)

Dans cet inventaire, j'ai renseigné le chemin de la clef SSH a utiliser pour qu'Ansible puisse se connecter. Cependant la clef publique n'est pas encore déployé sur les containers donc la connexion ne pourra pas se faire. 

En effet lorsque nous lançons le ping via une commande Ansible (ad-hoc) : 

![image](https://user-images.githubusercontent.com/51991304/143884797-f4fad515-df11-4184-9547-b76aba29dd02.png)


En copiant notre **clef publique** sur les containers pour autoriser notre user a se connecter via la clef privée défini dans le playbook. 

![image](https://user-images.githubusercontent.com/51991304/143885441-fec5a08d-c41d-4d6b-bd14-6587b701899e.png)


Nous arrivons à ping les containers via Ansible. 


![image](https://user-images.githubusercontent.com/51991304/143885509-10a5e667-69f8-48de-8fdb-dda80222011a.png)

Pour ping les machines Ansible, Ansible va, via le module Ping, vérifier si un interpreter Python est bien présent sur la machine. 
Il ne s'agit pas d'un Ping ICMP. Cependant, le résultat du module Ping va être Pong uniquement si Ansible arrive à se connecter en SSH à la machine distante et si il trouve un interpretre Python **utilisable**.

## Exercice 2 : Préparation de l'environnement via Ansible

- Creer l’utilisateur user-ansible sur les nodes
  - Module user (Adhoc ou Playbook : https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html )
- Donner les droits sudo à user-ansible avec le module user
- Creer et pousser la clef publique de `user_ansible` pour pouvoir lancer les actions via cet utilisateur
  - Module authorized_keys : https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html
- Lancer une commande en sudo (*become*) avec l'utilisateur `user_ansible` : id ou whoami par exemple. 





