
#  TP6
 
## DEMASFRAND-PUISSANT

## Exercice 1 : Disques et partitions

1. Pour creer un nouveau disque, nous entrons dans la configuration de virtualbox.
2. Afin de vérifier si le disque est bien présent, on utilise la commande :

```
lsblk
```
3. Nous allons maintenant partitionner ce disque :
```
sudo fdisk /dev/sdb
```
Cette commande ouvre l'interface de création des partitions.
- Une partition de 2 Go de type Linux
```
command (m for help): new
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-10485759, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-10485759, default 10485759): +2G


```

- Une partition de 3 Go en NTFS

Nous allons creer une partition linux comme précédemment mais de 3 Go puis nous allons changer le type de la partition en NTFS dans un deuxième temps.

```
command (m for help): t
Partition number (1,2, default 2): 2
Hex code (type L to list all codes): 7
```
### Attention : a la fin de la configuration entrer w pour sauvegarder et quitter.

4. Nous allons maintenant formater les deux partition à l'aide de la commande mkfs :
- Partition 1 :
```
sudo mkfs.ext4 /dev/sdb1
```

- Partition 2 :
```
sudo mkfs.ntfs /dev/sdb2
```

5. La commande df -T qui affiche le type de système de fichier des partitions ne fonctionne pas sur notre disque car notre disque n'est pas monté sur les partitions.

6. Nous allons faire en sorte que les partition soient montées automatiquement au démarrage de la machine :
- Point de montage partition 1 : /data
Création du réperoire /data
```
sudo mkdir /data
```
Puis on se rend dans le fichier /etc/fstab et on ajoute la ligne :
```
/dev/sdb1 /data ext4 defaults 0 0
```

- Point de montage partition 2 : /win
Création du réperoire /data
```
sudo mkdir /win
```
Puis on se rend dans le fichier /etc/fstab et on ajoute la ligne :
```
/dev/sdb2 /win ntfs defaults 0 0
```

7. Pour mettre à jour la configuration :

```
sudo mount -a
```

## Exercice 2 : Personnalisation de GRUB

1.  Commenter le contenu de du fichier /etc/default/grub.d/50-curtin-settings.cfg

2. Modification de /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes.
```
GRUB_TIMEOUT=10
```
3. On lance la commande update-grub afin de prendre en compte nos changement dans GRUB.

4. On redémarre la VM est on a bien l'écran de GRUB.

5. On va changer la résolution de GRUB. On décommente la ligne et ajoute notre résolution dans /etc/default/grub.

```
GRUB_GFXMODE=1600x900
```

6. Nous allons maintenant ajouter un fond d'écran. Dans le fichier grub :

```
GRUB_BACKGROUND="/images/background.png"
```

7. Nous allons installer un theme pour grub.
Dans le fichier GRUB, ajouter :
```
GRUB_THEME="/boot/grub/themes/ubuntu-mate/theme.txt"
```

8.  Ajout d'une entrée permettant d’arrêter la machine, et une autre permettant de la redémarrer.
Rendons nous dans le fichier /etc/grub.d/40_custom et ajoutons ces lignes après le commentaire :

```
menuentry 'Arrêt du système' {
	halt
}
menuentry 'Redémarrage du système' {
	reboot
}

```

9. Configuration du clavier français :

```
LANG=fr_FR 
GRUB_TERMINAL_INPUT=at_keyboard
``` 

## Exercice 3 : Noyau

Nous allons creer un module pour le noyau.

1. Installation du paquet build-essential pour pouvoir compiler des programmes en C.

```
sudo apt install build-essential
```

2. Création du fichier hello.c

3. Création du makefile correspondant

4. Compiler le module à l’aide de la commande make, puis installez-le à l’aide de la commande make install.

5. Charger le module :

```
sudo modprobe -a hello
```
Pour visualiser le journal :

```
tail /var/log/syslog
```

6. Informations sur le module :

```
modinfo hello
```

7. Déchargement du module :

```
sudo modprobe -r hello
```
On peutr encore visualiser le journal des logs et voir que la fonction cleanup à été appelée.

8. Nous allons maintenant faire en sorte que le module soit chargé automatiquemennt quand le sytème démarre.

Nous ajoutons une ligne dans le fichier /etc/modules
```
hello 
```