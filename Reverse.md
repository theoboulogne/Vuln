# Reverse

## [Retour à l'index](https://github.com/theoboulogne/Vuln)

-----

**Toujours changer la config du ASLR :**
```powershell
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space 
```

-----

## Index

- [Static Reverse](#Static)
- [Dynamic Reverse](#Dynamic)

-----

## Static

-----

### Méthodologie

- Ouvrir le code avec Ghidra
- Rechercher les strings pour avoir des infos (dans Ghidra ou avec la commande suivante)
```powershell
strings FILE
```
- Comprendre et renommer les fonctions / variables
- En cas de problème utiliser un [compilateur C en ligne](https://godbolt.org/)

-----

## Dynamic

-----

### Commandes GDB

- Déassemble la fonction
```gdb
disas <function name>
```
- Ajoute un breakpoint à une adresse donnée
```gdb
b * <breakpoint address>
```
- Va à la prochaine instruction (EIP+1)
```gdb
ni
```
- Continue l'exécution jusqu'au prochain breakpoint
```gdb
c
```
- Lance ou relance le programme
```gdb
r
```
- Inspecte le registre
```gdb
i r
```
- Affiche des infos sur une fonction (start ici)
```gdb
info functions start
```
- Récupère l'adresse d'un JMP ESP
```gdb
jmp esp
```
- Affiche le contenu d'une adresse
```gdb
x/xw <register or address>
```
- Affiche le contenu du ESP
```gdb
x/200w $esp
```

-----

### Récupération d'informations auxilliaire

- Utiliser strace / ltrace puis regarder les informations données
- Utiliser wireshark en arrière plan pour récupérer le traffic

-----

### Méthodologie pour un stack overflow

- Trouver un point d'injection vulnérable
- Y mettre une longue chaine de caractère générée avec PwnTools
```powershell
pwn cyclic 400
```
- Récupérer l'adresse de plantage pour en déduire le nombre de caractère
```powershell
pwn cyclic –l ADDRESSE
```
- Mettre une chaine de la taille donnée suivi de l'adresse que l'on veut exécuter
- On peut aussi récupérer notre shellcode, pour ça on met une adresse contenant un JMP ESP
```gdb
jmp esp
```
- On rajoute ensuite des NOP
- Finalement on rajoute notre shellcode généré avec metasploit ou récupéré sur [internet](http://shell-storm.org/shellcode/)

