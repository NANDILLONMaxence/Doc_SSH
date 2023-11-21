# Doc_SSH

### Installation d'un serveur SSH et première connexion:
- Vérifier si OpenSSH est installé: `apt-cache policy openssh-server`
- Vérifier le statut d'OpenSSH: `systemctl status sshd`
- Redémarrer OpenSSH: `systemctl restart sshd`
- Première connexion: `ssh -p [port] [user]@[ip-server]`

### Modification de la configuration SSH:
- Ouvrir le fichier de configuration: `nano /etc/ssh/sshd_config`
- Autoriser la connexion root:
  - `PermitRootLogin yes` ou `PermitRootLogin without-password`
- Créer un utilisateur: `sudo adduser [nom_utilisateur]`
- Donner des droits root à un utilisateur:
  - Aller dans le fichier sudoers: `sudo visudo`
  - Ajouter cette ligne: `[nom_utilisateur] ALL=(ALL:ALL) ALL`
  - Ajouter une personne au groupe sudoers : `sudo adduser [user] -G sudo`
- Accorder des droits particuliers sur un fichier ou dossier: `sudo chown [nom_utilisateurPropriétaire]:[nom_utilisateurnouveaupropri] /chemin/vers/mon_repertoire`

### Création d'une liste blanche:
- Ouvrir le fichier de configuration SSH: `sudo nano /etc/ssh/sshd_config`
- Ajouter les utilisateurs autorisés: `AllowUsers [user1] [user2]`
- Redémarrer le service SSH: `sudo systemctl restart ssh`

### Création d'une liste blanche avec un groupe:
- Créer le groupe ssh_allowed: `sudo groupadd ssh_allowed`
- Ajouter des utilisateurs au groupe ssh_allowed: `sudo usermod -aG ssh_allowed [user1]` et `sudo usermod -aG ssh_allowed [user2]`
- Ouvrir le fichier de configuration SSH: `sudo nano /etc/ssh/sshd_config`
- Ajouter la ligne pour spécifier le groupe autorisé: `AllowGroups ssh_allowed`
- Redémarrer le service SSH: `sudo systemctl restart ssh`

### Création d'une liste noire:
- Créer le groupe ssh_not_allowed: `sudo groupadd ssh_not_allowed`
- Ajouter des utilisateurs au groupe ssh_not_allowed: `sudo usermod -aG ssh_not_allowed [user1]` et `sudo usermod -aG ssh_not_allowed [user2]`
- Ouvrir le fichier de configuration SSH: `sudo nano /etc/ssh/sshd_config`
- Ajouter la ligne pour spécifier le groupe non autorisé: `DenyGroups ssh_not_allowed`
- Pour refuser explicitement certains utilisateurs, ajouter: `DenyUsers [user-3] [user-6]`
- Redémarrer le service SSH: `sudo systemctl restart ssh`

### Gestion des logs OpenSSH:
- Consulter les journaux d'OpenSSH: `journalctl -u ssh`
- Afficher les logs OpenSSH des 50 dernières entrées: `journalctl -u ssh -n 50`

### Personnalisation de la bannière SSH:
- Modifier la bannière dans le fichier de configuration: `sudo nano /etc/ssh/sshd_config`
- Activer la bannière: `Banner /etc/ssh/banner.txt`
- Créer le fichier de bannière personnalisé: `sudo nano /etc/ssh/banner.txt`
- Ajouter le message personnalisé
- Redémarrer le service SSH: `sudo systemctl restart ssh`

### Afficher la dernière connexion de l'utilisateur:
- Modifier le fichier de bannière personnalisé: `sudo nano /etc/ssh/banner.txt`
- Ajouter les lignes pour afficher la dernière connexion: 
	```
	Bienvenue sur mon serveur SSH.
	Toutes les connexions sont surveillées et enregistrées.
	Dernière connexion :
	$(date)
	```
- Redémarrer le service SSH: `sudo systemctl restart ssh`

### Envoi de fichiers via SSH (SCP):
- Copier un fichier depuis la machine locale vers une machine distante:
`scp chemin_du_fichier_local@adresse_distante:chemin_de_destination`

- Copier un fichier depuis une machine distante vers la machine locale:

`scp utilisateur_dist@adresse_ip_distante:chemin_du_fichier_local_avec_le_fichier chemin_de_destination_locale`


### Installation d'OpenSSH sur Windows:
- Vérifier si OpenSSH est installé: `Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'`
- Installer le client OpenSSH: `Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0`

### Authentification SSH par clés:
- Générer une clé SSH: `ssh-keygen -t ed25519`
ou
-  Générer une clé SSH: `ssh-keygen -b 4096`
ou pour Ubuntu / Debian
-  Générer une clé SSH:ssh-keygen -t ecdsa -b 521 -N votre-mot-de-passe
- Votre clé se situe à cet emplacement: C:\Users\[utilisateur]/.ssh/ Sous le nom de id_rsa et id_rsa.pub
- Mettre la clé sur le serveur
- Pour Windows, éditer le fichier `~/.ssh/authorized_keys` sur le serveur
- Aller à l'emplacement: /home/user/.ssh/
Si le fichier .ssh ne s'affiche pas, exécutez cette commande pour le chercher dans le système: `find / .ssh`
Si .ssh n'existe toujours pas, créez le dossier à l'emplacement /home/user/ de l'utilisateur: `sudo mkdir .ssh`
Copiez/collez le contenu de la clé publique dans le dossier .ssh: `scp chemin_de_la_cle_publique.pub utilisateur@adresse_du_serveur:~/.ssh/`
   ```
   Exemple : `scp C:\Users\maxma\.ssh\id_rsa_maxence.pub maxence@192.168.1.100:~/.ssh/`
   ```
Créez un fichier (sans extension) `authorized_keys` dans .ssh: `sudo touch authorized_keys`
Ouvrez le fichier `authorized_keys`, par exemple avec: `nano ~/.ssh/authorized_keys`
Collez votre clé publique dans le fichier en cliquant avec le bouton droit de la souris. Le résultat ressemble à ceci:
  ```
  ssh-ed25519 AAAAC3NzaC1lZBI1NTE5AAAAIT5Sem9ensPAP1BMO32bXcWtl1gt0ZedNz/cpcTr3R+D ed25519-key-20210217
  ```
Enregistrez ensuite les modifications et fermez le fichier ( ctrl + x > y > enter ).
Restreignez les droits à ce dossier: `chmod 600 ~/.ssh -Rf`
Pour tester la clé, allez dans le fichier config sshd: `nano /etc/ssh/sshd_config` :
Activez : `PubkeyAuthentication yes` & `PasswordAuthentication no` 
Relancez le système: `systemctl restart ssh`
Ouvrez Tabby et connectez-vous: `ssh -i ~/.ssh/id_rsa_maxence maxence@192.168.1.100` ou `ssh [Host]`

### Utilisation de KeeAgent:
- Téléchargez et installez Keepass: [lien](https://keepass.info/download.html)
- Téléchargez KeeAgent: [lien](https://lechnology.com/software/keeagent/)
- Installez KeeAgent dans Keepass:
Tools > Plugins > Open Folder
Ouvrez l'archive KeeAgent et glissez le KeeAgent.plgx dans le dossier Plugins
Redémarrez Keepass
- Configurez KeeAgent dans les options de Keepass:
Add entry > Advanced > Attach File(s)… importer vos clés RSA > KeeAgent > cochez Allow KeeAgent > Attachment sélectionnez la RSA pas la .pub (si vous avez une clé de gestion, cliquez dessus) > OK.
Vérifiez quelles clés sont chargées et utilisez Outils > KeeAgent.
- Activez OpenSSH sur Windows dans les options de Keepass:
Tools > Options > KeeAgent > Enable agent for Windows OpenSSH.
- Activez OpenSSH au démarrage du service: `net stop ssh-agent` puis `net start ssh-agent`

### En plus:
Activation d'Openssh au démarrage service > Openssh Authentification Agent > clic droit > propriété > Type de démarrage Automatique.
Pour redémarrer Openssh Server Agent: `net stop ssh-agent` puis `net start ssh-agent`

### Commande SSH avec Tunneling (Port Forwarding)

La commande `ssh` est utilisée pour établir une connexion SSH sécurisée. Dans cet exemple, nous utilisons la commande `ssh` avec des options pour créer un tunnel SSH local.

- `ssh`: C'est la commande principale pour établir une connexion SSH.

- `-f`: Cette option indique à SSH de passer en arrière-plan après l'authentification, permettant ainsi de continuer à utiliser le terminal pour d'autres tâches.

- `maxence@192.168.1.100`: C'est l'adresse de l'hôte distant auquel vous vous connectez en tant qu'utilisateur "maxence". Vous devez remplacer ces valeurs par les vôtres.

- `-L 22:localhost:80`: Cette option définit le tunnel SSH local. Elle redirige le port 22 de l'hôte distant vers le port 80 de votre machine locale. Ainsi, le port 80 de l'hôte distant sera accessible localement via le port 22 (SSH) de votre machine.

- `-N`: Cette option indique à SSH de ne pas exécuter de commande distante après l'authentification. Elle permet d'établir la connexion uniquement pour créer le tunnel, sans exécuter d'autres commandes sur l'hôte distant.

Cette commande est utile pour accéder de manière sécurisée à un service web distant (port 80) en utilisant un tunnel SSH local (port 22) pour le transfert de ports.

Exemple d'utilisation :
```shell
ssh -f maxence@192.168.1.100 -L 22:localhost:80 -N
```


### SSHFS-Win SSHFS pour Windows

- Installer sur le serveur SSHFS : `sudo apt install sshfs`

Monter un répertoire distant :
Vous pouvez utiliser SSHFS pour monter un répertoire distant sur votre système local. 
1. Remplacez <utilisateur_distant> par le nom d'utilisateur du serveur distant,
  - <hôte_distant> par l'adresse IP ou le nom d'hôte du serveur distant,
  - <répertoire_distant> par le chemin du répertoire que vous souhaitez monter.
  - De plus, remplacez <point_de_montage_local> par le chemin où vous souhaitez monter le répertoire distant localement.
	```
	sshfs <utilisateur_distant>@<hôte_distant>:<répertoire_distant> <point_de_montage_local>
	```

Lien Github : https://github.com/winfsp/sshfs-win

Installation : 
`winget install -h -e --id "WinFsp.WinFsp" && winget install -h -e --id "SSHFS-Win.SSHFS-Win"`

### Utilisation :
Une fois que vous avez installé WinFsp et SSHFS-Win, vous pouvez mapper un lecteur réseau à un répertoire sur un hôte SSHFS à l'aide de l'Explorateur
 - Explorateur de fichier > Clic droit sur Ce PC > Connecter un lecteur Reseau...
 - Choisissez la lettre du lecteur 
 - Dans dossier : \\sshfs\USER@IP-SERVEUR

La première fois que vous mappez un chemin SSHFS particulier, vous serez invité à saisir le nom d'utilisateur et le mot de passe SSHFS.
Vous pouvez choisir d'enregistrer ces informations d'identification avec Windows Credential Manager, auquel cas vous ne serez plus invité au faire.

Pour démapper le lecteur, cliquez avec le bouton droit sur l'icône du lecteur dans l'Explorateur Windows et sélectionnez Déconnecter.
