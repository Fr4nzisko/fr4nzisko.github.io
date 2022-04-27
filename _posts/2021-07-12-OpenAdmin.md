---
title: "OpenAdmin - HackTheBox"
layout: single
excerpt: "Esta máquina es de dificultad fácil, me gusto más la intrusión al aprovecharme de un panel de control vulnerable llamado OpenNetAdmin, use un exploit que te explotaba la vulnerabilidad del panel y te otorgaba ejecución remota de código arbitrario. Esta vez la escalada fue bastante sencilla de completar, al hacer `sudo -l` me permitía como cualquier usuario ejecutar el binario de `nano` a un archivo llamado `priv`."
header:
show_date: true
classes: wide
header:
  teaser: "https://user-images.githubusercontent.com/69093629/125315417-67da5380-e337-11eb-844d-ab46b6686a1a.png"
  teaser_home_page: true
  icon: "https://user-images.githubusercontent.com/69093629/125662338-fd8b3b19-3a48-4fb0-b07c-86c047265082.png"
categories:
  - HackTheBox
tags:
  - OpenNetAdmin
  - gobuster
  - linpeas
  - ssh2john
---

<p align="center">
<img src="https://user-images.githubusercontent.com/69093629/125315417-67da5380-e337-11eb-844d-ab46b6686a1a.png">
</p>

Comencé haciendo un escaneo con `Nmap` para detectar puertos abiertos.

```bash
┌─[Fr4nzisko@RedTeam]─[/home/Fr4nzisko/Desktop]
└──╼ nmap -sS --min-rate=5000 -p- -v -Pn -n 10.10.10.171 -oG allports 
Starting Nmap 7.80 ( https://nmap.org ) at 2021-07-11 18:33 CEST
Initiating SYN Stealth Scan at 18:33
Scanning 10.10.10.171 [65535 ports]
Discovered open port 80/tcp on 10.10.10.171
Discovered open port 22/tcp on 10.10.10.171
Increasing send delay for 10.10.10.171 from 0 to 5 due to max_successful_tryno increase to 4
Completed SYN Stealth Scan at 18:33, 13.67s elapsed (65535 total ports)
Nmap scan report for 10.10.10.171
Host is up (0.049s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.81 seconds
           Raw packets sent: 69669 (3.065MB) | Rcvd: 69574 (2.785MB)
```

Luego efectúe otro escaneo de la versión y servicios que estaban corriendo en cada puerto encontrado.

```bash
┌─[Fr4nzisko@RedTeam]─[/home/Fr4nzisko/Desktop]
└──╼ nmap -sC -sV -p22,80 10.10.10.171 -oN targeted       
Starting Nmap 7.80 ( https://nmap.org ) at 2021-07-11 18:33 CEST
Nmap scan report for 10.10.10.171
Host is up (0.042s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:98:df:85:d1:7e:f0:3d:da:48:cd:bc:92:00:b7:54 (RSA)
|   256 dc:eb:3d:c9:44:d1:18:b1:22:b4:cf:de:bd:6c:7a:54 (ECDSA)
|_  256 dc:ad:ca:3c:11:31:5b:6f:e6:a4:89:34:7c:9b:e5:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.90 seconds
```

Tenía dos puertos abiertos, uno era un servidor web y el otro `SSH`, el servidor web traía la página de apache por defecto por lo que recurrí a hacer un fuzzing para encontrar directorios, antes de utilizar `gobuster` hice uso del script `http-enum` de `Nmap` que hace uso de un diccionario mucho más pequeño.

```bash
┌─[Fr4nzisko@RedTeam]─[/home/Fr4nzisko/Desktop]
└──╼ nmap --script http-enum 10.10.10.171          
Starting Nmap 7.80 ( https://nmap.org ) at 2021-07-11 18:34 CEST
Nmap scan report for 10.10.10.171
Host is up (0.066s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 12.02 seconds
```

Como no me encontró nada hice uso de `gobuster`, me encontró tres directorios interesantes.

![segunda (1)](https://user-images.githubusercontent.com/69093629/125317595-69a51680-e339-11eb-8d20-3cf019af1004.png)

![cuarta (1)](https://user-images.githubusercontent.com/69093629/125317656-76296f00-e339-11eb-8873-a611a8063fd2.png)

![tercer](https://user-images.githubusercontent.com/69093629/125317713-82adc780-e339-11eb-8c0b-1eaba055bac2.png)

En la tercera página, al hacer clic en `login` te redirigía a un panel.

![login](https://user-images.githubusercontent.com/69093629/125317855-9b1de200-e339-11eb-9bcd-cd5033b0e51c.png)

Probé a ver el código fuente de la página para ver como se llamaba el panel y buscar si hay algún exploit.

![codigofuente](https://user-images.githubusercontent.com/69093629/125318036-c6083600-e339-11eb-991e-7aa1a213c770.png)

Se llamaba `OpenNetAdmin`, me fui a `seachsploit` y encontré un exploit que me permitía ganar ejecución de código arbitrario.

![opennetadmin](https://user-images.githubusercontent.com/69093629/125318224-f4861100-e339-11eb-9697-c494d4bb9787.png)

Lo ejecuté dando los parámetros que me solicitaba y me otorgo una Shell.

![ona](https://user-images.githubusercontent.com/69093629/125318381-141d3980-e33a-11eb-90ca-69ba380e259d.png)

Esta Shell no me permitía hacer un tratamiento de la `TTY`  y encima no funcionaba para volver directorios atras y permanecer, porque te ejecuta un comando y se reinicia, por lo que pensé en entablarme una reverse Shell por netcat desde la que tenía.

![netcat](https://user-images.githubusercontent.com/69093629/125318568-3f078d80-e33a-11eb-93ea-f7ca3e763b0a.png)

Ahora ya me dejo hacer el tratamiento de la `TTY` y poder manejarme de una manera mucho más cómoda, era el usuario `www-data` por lo que no tenía tantos privilegios, al ir a `home` vi que habían dos usuarios, uno llamad `jimmy` y otro llamado `joanna`, entonces pensé en que me tenía que convertir en uno y luego en el otro y ya después hacer la escalada, tras una pequeña investigación encontré un archivo que tenía credenciales.

![credenciales (1)](https://user-images.githubusercontent.com/69093629/125319062-bb01d580-e33a-11eb-8261-303d539c856d.png)

Probé a autenticarme en `SSH` como `joanna` y no funciono, pero cuando probé con `jimmy` i si me autentico, ahora tenía que convertirme en el usuario `joanna`, me descargue `linpeas.sh` de GitHub y me abrí un servidor por Python para transferirlo a la máquina víctima, `linpeas` me encontró que había puertos abiertos en la máquina internamente, antes de recurrir a hacer `port forwarding`para ver que tenía cada puerto, seguí investigando un poco más hasta que encontré un archivo  PHP en la ruta `/var/www/internal` que devolvia la id_rsa en texto claro, viendo lo que tenía hasta ahora, pense en que quizas ese archivo estaba corriendo por el puerto que me encontró `linpeas` el `52847`, hice un `curl` a ese archivo llamado `main.php` desde la máquina víctima y me reporto la `id_rsa` en texto claro, pero estaba encriptada, hice uso de `ssh2john` para que me devuelva hash equivalente.

```bash
┌─[Fr4nzisko@RedTeam]─[/home/Fr4nzisko/Desktop]
└──╼ python /usr/share/john/ssh2john.py id_rsa 
id_rsa:$sshng$1$16$2AF25344B8391A25A9B318F3FD767D6D$1200$906d14608706c9ac6ea6342a692d9ed47a9b87044b94d72d5b61df25e68a5235991f8bac883f40b539c829550ea5937c69dfd2b4c589f8c910e4c9c030982541e51b4717013fafbe1e1db9d6331c83cca061cc7550c0f4dd98da46ec1c7f460e4a135b6f1f04bafaf66a08db17ecad8a60f25a1a095d4f94a530f9f0bf9222c6736a5f54f1ff93c6182af4ad8a407044eb16ae6cd2a10c92acffa6095441ed63215b6126ed62de25b2803233cc3ea533d56b72d15a71b291547983bf5bee5b0966710f2b4edf264f0909d6f4c0f9cb372f4bb323715d17d5ded5f83117233976199c6d86bfc28421e217ccd883e7f0eecbc6f227fdc8dff12ca87a61207803dd47ef1f2f6769773f9cb52ea7bb34f96019e00531fcc267255da737ca3af49c88f73ed5f44e2afda28287fc6926660b8fb0267557780e53b407255dcb44899115c568089254d40963c8511f3492efe938a620bde879c953e67cfb55dbbf347ddd677792544c3bb11eb0843928a34d53c3e94fed25bff744544a69bc80c4ffc87ffd4d5c3ef5fd01c8b4114cacde7681ea9556f22fc863d07a0f1e96e099e749416cca147add636eb24f5082f9224e2907e3464d71ae711cf8a3f21bd4476bf98c633ff1bbebffb42d24544298c918a7b14c501d2c43534b8428d34d500537f0197e75a4279bbe4e8d2acee3c1586a59b28671e406c0e178b4d29aaa7a478b0258bde6628a3de723520a66fb0b31f1ea5bf45b693f868d47c2d89692920e2898ccd89710c42227d31293d9dad740791453ec8ebfb26047ccca53e0a200e9112f345f5559f8ded2f193feedd8c1db6bd0fbfa5441aa773dd5c4a60defe92e1b7d79182af16472872ab3c222bdd2b5f941604b7de582b08ce3f6635d83f66e9b84e6fe9d3eafa166f9e62a4cdc993d42ed8c0ad5713205a9fc7e5bc87b2feeaffe05167a27b04975e9366fa254adf511ffd7d07bc1f5075d70b2a7db06f2224692566fb5e8890c6e39038787873f21c52ce14e1e70e60b8fca716feb5d0727ac1c355cf633226c993ca2f16b95c59b3cc31ac7f641335d80ff1ad3e672f88609ec5a4532986e0567e169094189dcc82d11d46bf73bc6c48a05f84982aa222b4c0e78b18cceb15345116e74f5fbc55d407ed9ba12559f57f37512998565a54fe77ea2a2224abbddea75a1b6da09ae3ac043b6161809b630174603f33195827d14d0ebd64c6e48e0d0346b469d664f89e2ef0e4c28b6a64acdd3a0edf8a61915a246feb25e8e69b3710916e494d5f482bf6ab65c675f73c39b2c2eecdca6709188c6f36b6331953e3f93e27c987a3743eaa71502c43a807d8f91cdc4dc33f48b852efdc8fcc2647f2e588ae368d69998348f0bfcfe6d65892aebb86351825c2aa45afc2e6869987849d70cec46ba951c864accfb8476d5643e7926942ddd8f0f32c296662ba659e999b0fb0bbfde7ba2834e5ec931d576e4333d6b5e8960e9de46d32daa5360ce3d0d6b864d3324401c4975485f1aef6ba618edb12d679b0e861fe5549249962d08d25dc2dde517b23cf9a76dcf482530c9a34762f97361dd95352de4c82263cfaa90796c2fa33dd5ce1d889a045d587ef18a5b940a2880e1c706541e2b523572a8836d513f6e688444af86e2ba9ad2ded540deadd9559eb56ac66fe021c3f88c2a1a484d62d602903793d10d
```

Copie el `hash` y lo pegue en un archivo nuevo llamado `hashs` para hacerle fuerza bruta con `john the ripper`.

```bash
┌─[Fr4nzisko@RedTeam]─[/home/Fr4nzisko/Desktop]
└──╼ john --wordlist=/usr/share/wordlists/rockyou.txt hashs
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
bloodninjas      (id_rsa)
1g 0:00:00:05 DONE (2021-07-12 16:10) 0.1855g/s 2660Kp/s 2660Kc/s 2660KC/sie168..*7¡Vamos!
Session completed
```

Le di permisos `600` a la id_rsa y probe a autenticarme con el usuario `joanna` por SSH desde la máquina víctima y funciono.

![id_rsa2 (1)](https://user-images.githubusercontent.com/69093629/125322013-922f0f80-e33d-11eb-9804-f85f1f2ae6b0.png)

Ya pude visualizar la `flag` del usuario, ahora solo faltaba la escalada de privilegios.

![user](https://user-images.githubusercontent.com/69093629/125322194-c9052580-e33d-11eb-96e0-3c2ca8a287d5.jpg)

<hr>
<h1 align="center"><b>ESCALADA DE PRIVILEGIOS</b></h1>

Haciendo `sudo -l` como todos los usuarios me permitía ejecutar `nano` con `sudo` a un archivo llamado `priv`.

![priv](https://user-images.githubusercontent.com/69093629/125322700-56487a00-e33e-11eb-894f-c374c5c7ad74.png)

Me dirigí a [gftobins](https://gftobins.github.io) y encontré una manera de aprovecharme de `nano` para la escalada.

![Captura de pantalla (655)](https://user-images.githubusercontent.com/69093629/125322596-3dd85f80-e33e-11eb-86cd-c85a76726d85.png)

Hice lo pasos que me pedía y consegui `root`.

![padentros](https://user-images.githubusercontent.com/69093629/125322935-90b21700-e33e-11eb-9a0b-f3ff219cf2c0.png)







