augmented_sandbox
=================

Bac à sable augmenté du muséolab  
Fork de ce projet
http://idav.ucdavis.edu/~okreylos/ResDev/SARndbox/ 
https://arsandbox.ucdavis.edu/

# Matériel

https://arsandbox.ucdavis.edu/instructions/hardware-2/

- PC installé avec Ubuntu (config mini : 2GB RAM, freq proc : 3GHZ, Nvidia 970, Port HDMI)
- 1 Kinect V1
- 1 projecteur courte focale
- 1 bac à sable
- du sable

# Installation / Démarrage
suivre les instructions détaillées ici : http://idav.ucdavis.edu/~okreylos/ResDev/SARndbox/index.html
___
Pour installer une version précédente, la procédure est la même, sauf :
- les versions de VRui et de l'outil Kinect doivent être en phase : pour la SAndbox 2.5, il faut aller chercher les versions *Vrui-4.5-004* et *Kinect-3.5*
- Pour télécharger et compiler *Vrui-4.5-004*, il faut modifier le script /Install VRui/Build-Ubuntu.sh comme suit (lignes 37/38) afin de forcer les versions
```
# wget -O - http://idav.ucdavis.edu/~okreylos/ResDev/Vrui/Vrui-$VRUI_VERSION-$VRUI_RELEASE.tar.gz | tar xfz -
# cd Vrui-$VRUI_VERSION-$VRUI_RELEASE
wget -O - http://idav.ucdavis.edu/~okreylos/ResDev/Vrui/Vrui-4.5-004.tar.gz | tar xfz -
cd Vrui-4.5-004
```
___

## Calibration de la caméra (Step 8/9)
![Vidéo de calibration 4:10](https://www.youtube.com/watch?v=EW2PtRsQQr0&feature=youtu.be&t=4m10s)

Lors des étapes de calibration de la caméra  et , et de la projection, l'idée est la suivante :
- Il faut d'abord affecter une touche (ou deux pour la calibration de la projection) à une fonction, puis utiliser cette fonction

1. Démarrer RawKinectViewer (*Step 7 de la procédure*) : 
```
cd ~/augmented_sandbox/src/SARndbox-2.5
RawKinectViewer -compress 0
```
2. Appuyer sur une touche non affectée (par exemple : **[1]** ) et bouger la souris en même temps (Step 8 de la procédure)
3. Choisir dans le menu la fonction correspondante : **Extract Plane**
4. Appuyer sur **[1]** et tirer sur l'écran le rectangle correspondant aux quatre coins supérieurs du bac (Attention, pas de retour visuel, bien checker ce qu'il se passe dans la console)
5. Dans la console, apparait l'équation du plan de la caméra sous la forme : 
```Camera-space plane equation: x * <some vector> = <some offset>```
6. Editer le fichier de configuration (nano etc/SARndbox-2.6/BoxLayout.txt) et recopier dans le fichier l'équation sous la forme : 
```<some vector>, <some offset>```
7. Sélectionne une touche **[2]**, bouger la souris, choisir la fonction **Measure 3D Positions**
8. Cliquer sur l'écran les 4 coins dans l'ordre suivant : Bas-Gauche, Bas-Droite, Haut-Gauche, Haut-Droite
9. Continuer l'édition du fichier de configuration (*nano etc/SARndbox-2.6/BoxLayout.txt*) et copier-coller les coordonées
10. C'est fini, le fichier doit ressembler à ça. Enregistrez le.
```
(-0.0076185, 0.0271708, 0.999602), -98.0000
(  -48.6846899089,   -36.4482382583,   -94.8705084084)
(   48.3653058763,   -34.3990483954,   -89.3884158982)
(   -50.674914634,    35.8072086558,   -97.4082571497)
(   48.7936140481,    36.4780970044,     -91.74159795)
```

### Calibration du bac à sable
![Vidéo de calibration 10:10](https://www.youtube.com/watch?v=EW2PtRsQQr0&feature=youtu.be&t=10m10s)

Ensuite, il faut calibrer la projection + l'amplitude de hauteur de sable dans le bac.
Le bac doit être rempli de sable.
Essayez de créer un relief avec des points hauts et des points bas (Ex : une moitié du bac trés haute, une autre moitié trés basse)

1. Régler le projecteur pour que l'image recouvre la totalité du bac.
2. Vous aurez besoin d'une mire, l'outil de calibration attend un disque blanc de 120 mm. C'est précisément la taille d'un CD recouvert de papier. Tracez-y une croix au centre.
Vous ne pouvez pas tenir le disque à la main pour la calibration, ajouter un manche en fil de fer.
3. Démarrer l'outil de calibration en renseignant la résolution de l'image projetée
```
cd ~/src/SARndbox-2.6
./bin/CalibrateProjector -s <width> <height>
```
4. Aprés une image rouge, apparait un fond noir, avec une croix blanche et un rectangle jaune.
5. Mettre l'image en plein écran **[F11]**
6. Même combat que pour les outils précédents, il faut binder 2 touches. Appuyer sur une touche (Par ex. **[c]**) et bouger la souris. Choisir la fonction **Capture** en cliquant dans le menu. Un popup doit alors apparaitre, appuyer sur une seconde touche différente (par ex. **[v]**)
Ces touches vont vous permettre de calibrer la projection et les hauteurs de sable du bac
7. Positionner le disque en faisant correspondre la croix blanche avec la croix du disque.
8. Vous voyez à présent votre main et le disque. Si votre main n'apparait pas au bon endroit, pas de panique, aprés la calibration tout rentrera dans l'ordre.
Le disque va être détecté par la Kinect, une fois détecté il apparait en vert et votre main en jaune. Si vous tenez le disque à la main, la Kinect ne peut pas les différencier et la calibration ne peut pas se faire. 
9. Une fois le disque apparu en vert et les croix alignées, cappuyer sur la touche choisie (**[c]**). Aprés un temps de pause, la croix blanche se déplace.
Vous devez maitenir le disque en position vert pendant 2 secondes.
Ce message apparait alors dans la console.
```
CalibrateProjector: Capturing 60 tie point frames...done
```
10. Répéter l'opération
11. Si la calibration est ok, un message de ce type apparait dans la console.
```
  -323.634      -16.502      856.369       123602
   59.6915     -480.355      342.951      60259.6
  0.146643   -0.0209159     0.988968      162.584
RMS calibration residual: 10.2281
Z range of collected tie points: [-0.0220997, -0.0187916]
```
12. Si ce message apparait, ce n'est pas bon. Il faut alors reprendre la calibration, la croix blanche revient au premier point.
```
Calibration error: Some tie points have negative projection weights. Please start from scratch
```
# Utilisation
Creusez des reliefs pour faire apparaitre les nappes phréatiques.
Formez des montagnes pour faire apparaître la neige.

Mettre la main ouverte au dessus du bas permet de faire pleuvoir :D !

