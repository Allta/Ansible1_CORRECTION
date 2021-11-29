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


Pour cet exercice, j'utilise un playbook qui va permettre d'écrire un jeu d'instruction (ou de tâches) qu'Ansible va ensuite executer sur les différentes machines présentes dans le groupe de l'inventaire. 


![image](https://user-images.githubusercontent.com/51991304/143890178-4997a935-5fb5-4905-9377-a62bae84f9ce.png)

Dans chaque playbook Ansible, certaines informations sont obligatoires 

- le nom du groupe sur lequel les taches seront executées
- les différentes tâches à executer

Les playbook Ansible permettent de faire le lien entre le fichier inventaire et les tâches. 

![image](https://user-images.githubusercontent.com/51991304/143890608-5dadc94f-9326-421d-8865-59152decb4d1.png)


Pour exécuter le playbook, il faut lancer la commande `ansible-playbook`. 

Je renseigne le flag `-K`  qui permet de renseigner le mot de passe de l'utilisateur utilisé par Ansible. :

![image](https://user-images.githubusercontent.com/51991304/143890911-f60814ea-9ddb-4506-a2e6-d9f7ae8dbca2.png)

Le module `become` dans le playbook nous oblige a renseigner le mot de passe sudo vu qu'il n'y a pas de configuration `NOPASSWD` sur nos machines distantes dans le fichier `sudoers`.


La première tâche du playbook permet de créer un utilisateur avec le module user.
Les arguments permettent de rajouter cet utilisateur dans le groupe **sudo** sans supprimer les groupes d'appartennance de l'utilisateur (grâce à `append`).

La seconde tâche permet de rajouter la clef **publique** présent sur notre machine Ansible dans les machines distantes. Et d'associer cette clef publique à l'utilisateur fraichement créer. 

Pour cela nous utilisons le module https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html. 
Il suffit de spécifier l'utilisateur sur lequel nous souhaitons binder notre clef publique, le **state** (paramètre présent dans de nombreux modules qui permet de spécifier si un fichier/service ou autre doit être présent ou supprimé du systeme distant. Il peut aussi être utilisé pour gérer l'état des services via systemctl). 

Et finalement, il faut renseigner la clef. Ici il faut utiliser une syntaxe particulière (décrit dans la documentation).

La syntaxe utilisé est du **Jinja2** qui permet d'utiliser des templates dans les playbook Ansible.

**lookup** est un **plugin** qui permet d'aller chercher des fichiers en dehors des sources ansible : https://docs.ansible.com/ansible/latest/plugins/lookup.html 


Maintenant que notre utilisateur est crée nous pouvons nous connecter dessus à l'aide de notre clef privée.

![image](https://user-images.githubusercontent.com/51991304/143894856-9ab194b2-a357-48cf-8884-9d8a9cfcd904.png)

Nous voyons que la clef privée fonctionne et que notre utilisateur est bien présent dans le groupe **sudo**. 

Pour lancer une commande avec un autre utilisateur que celui prévu par défaut dans notre fichier inventaire, nous utilisons le module **become** qui permet de devenir un autre utilisateur.

_**Source :**_ https://serverfault.com/a/741058


![image](https://user-images.githubusercontent.com/51991304/143895064-1f338e6d-4378-4efe-b3a1-4f483afcedd3.png)



Lorsque nous essayons de lancer une commande avec notre utilisateur nous avons une erreur. Ansible n'arrive pas à gérer le passage à un utilisateur sans privilège. 

Il y a un soucis lors de la lecture des fichiers temporaires qui font fonctionner Ansible. 

![image](https://user-images.githubusercontent.com/51991304/143892490-94d9e0ff-6092-49e3-8e2a-e874e1bad2d6.png)





Une recherche google nous informes qu'il manque le paquet **acl** d'installer sur nos machines clients. 

_**Source : **_ https://github.com/georchestra/ansible/issues/55


Pour corriger cela nous rajoutons une tâche d'installation de ce paquet. 

![image](https://user-images.githubusercontent.com/51991304/143895568-d9932dd9-2ac7-489a-b5c5-a1cd187f61ef.png)


Maintenant nous pouvons lancer notre playbook. Cependant nous nous apercevons que la commande est exécutée mais nous n'avons pas l'output de cette commande.

En effet Ansible ne récupère pas l'output des commandes exécutées sur les machines distantes. 

Pour cela soit nous pouvons créer une variable et récupérer l'output et l'afficher dans cette variable grâce au modue **debug** : 

https://stackoverflow.com/questions/20563639/ansible-playbook-shell-output

![image](https://user-images.githubusercontent.com/51991304/143895875-23459deb-0d78-40a3-a15d-b87a6404d951.png)



Cela nous donne : 


![image](https://user-images.githubusercontent.com/51991304/143895938-7c1adb25-e38e-40e2-8db4-005c094aaf0d.png)



