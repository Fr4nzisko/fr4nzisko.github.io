---
title: "Basic linux commands Part 1"
layout: single
excerpt: "Esta es la primera version de mis apuntes sobre la terminal de Linux."
header:
show_date: true
classes: wide
header:
  teaser: "https://img.freepik.com/foto-gratis/foto-monitor-pc-escritorio-funciones-variables-objetos-javascript-gerentes-proyecto-trabajan-nueva-idea-proceso-creacion-tecnologia-futura_372999-255.jpg"
  teaser_home_page: true
  icon: "https://cdn-icons-png.flaticon.com/512/6124/6124995.png"
categories:
  - Linux
tags:
  - cd
  - pwd
---

<p align="center">
<img src="https://img.freepik.com/foto-gratis/foto-monitor-pc-escritorio-funciones-variables-objetos-javascript-gerentes-proyecto-trabajan-nueva-idea-proceso-creacion-tecnologia-futura_372999-255.jpg">
</p>

# Basic linux commands Part 1

## Basic concepts


**Command showing the user name:**

````bash
whoami
````

**Command that shows in which directory (folder) the user is located.:**

````bash
pwd
````

**Host Name**
````bash
hostname
````

**concatenate two commands**

````bash
whoami; pwd; hostname
````


**Command that lists files and folders in the directory:**

````bash
ls
````

**Command that lists files and folders in the directory, including the hidden ones:**


````bash
ls -a
````

**Command listing files and folders in the directory with information on the size and creation date of each file:**

````bash
ls -lh
````

**Command that lists files and folders in the directory with information about the size and creation date of each file, including hidden files:**


````bash
ls -lha
````

**Command that clears the terminal screen:**

````bash
clear
````

## Create, edit, move and delete files


**Creating a text file:**


````bash
echo "Frase que quiero dentro de mi archivo de texto llamado message.txt" > message.txt
````

**Open a text file:**

````bash
cat menssage.txt
````

**Adding a text line to the message.txt file:**

````bash
echo "La frase NÚMERO 2 que quiero dentro de mi archivo de texto llamado message.txt" >> mensaje.txt
````

**Delete a file:**

````bash
rm NombreDeArchivo.extensión
````

**Move a file:**

````bash
mv NombreDeArchivo.extensión /Directorio/Destino/
````


## Creating, Deleting and Navigating between directories (folders):


**Accessing a directory:**

````bash
cd nombreDirectorio
````

**Exit the directory and return to the previous one (go up one level).:**


````bash
cd ..
````

**Exit and go to the root directory:**


````bash
cd
````
**Creating a directory:**


````bash
mkdir NombreNuevoDirectorio
````

**Delete a directory:**

````bash
rmdir NombreDirectorio
````

**Delete a directory with files:**


````bash
rmdir -r NombreDirectorio
````

## file editors

**File creation**

````bash
touch
````


**notepad**

````bash
nano
````


**notepad**

````bash
vim
````


**show content**

````bash
more
````

**determine the type and format of a file**
````bash
file
````
## Count the words, characters and line breaks contained in a file.

**Count characters**
````bash
wc -c
````


**Counting lines**
````bash
wc -l
````

##

**Redirects stdout to a file. Creates it if it does not exist, if it does exist it overwrites it**

````bash
>
````


**add the output**

````bash
>>
````


**redirects the output**

````bash
<
````

By Fr4nzisko



