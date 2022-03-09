# Android

## [Retour à l'index](https://github.com/theoboulogne/Vuln)

-----

## Index

- [Static](#Static)
- [Dynamic](#Dynamic)

-----

### Connexion en root sur le téléphone

```powershell
adb root  
adb connect ip:port (de blue stacks) 
adb shell  
su 
```
- Prérequis :
```powershell
bluestack.conf -> ctrl+f root -> les passer à 1 
```
- IP par défaut :
```powershell
192.168.56.1
```

-----

## Static

-----

### Méthodologie pour JADX

- Ouvrir jadx :
```powershell
jadx-gui
```
- Puis, Fichier -> Ouvrir l'APK

-----

### Méthodologie pour un backup
```powershell
adb install APPLI.apk
adb start-server
adb devices
```
- On vérifie que notre émulateur apparait bien puis on fait lance le backup
```powershell
adb backup -apk -shared -all -f backup.ab
```
- Ensuite on accepte le backup sans mettre de mot de passe sur l'émulateur puis on décompresse
```
( printf "\x1f\x8b\x08\x00\x00\x00\x00\x00" ; tail -c +25 backup.ab ) |  tar xfvz -
```
- Il ne reste plus qu'à trouver des données intéressantes

-----

## Dynamic

-----

### Méthodologie pour une Injection SQL

- On peut identifier des champs injectables avec jadx
- Utiliser tout les champs qui semblent injectables (reprendre la [méthodologie Web](https://github.com/theoboulogne/Vuln/blob/main/Web.md#SQL-Injection))

-----

### Méthodologie pour récupérer des informations auxilliaire

- Utiliser la commande suivante dans un terminal et regarder si on intercepte des données non sécurisées en réalisant des actions sur l'appli
```powershell
adb logcat
```

-----

### Méthodologie pour lancer une activité exportée

- On récupère les activités lançables avec jadx dans le Manifest.
- Si on a pas de paramètre on fait la commande suivante
```powershell
adb shell am start -n PACKAGE-COM-A-METTRE/ACTIVITY-COM-A-METTRE 
```
- Si on a des paramètres (extras) on fait la commande suivante
```powershell
adb shell am start -n com.medium.reader/com.medium.android.donkey.save.SaveToMediumActivity -e android.intent.extra.TEXT “https://attacker.com" 
```

