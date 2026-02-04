# Introducción al HPC y Linux

## 1. ¿Qué es un HPC?

Un **HPC (High Performance Computing)** es un conjunto de computadoras (llamadas **nodos**) que trabajan juntas para realizar cálculos intensivos.

En este curso, el HPC se utilizará para:
- Analizar datos genómicos y de genómica de poblaciones
- Ejecutar scripts y pipelines que serían muy lentos en una laptop personal
- Aprender flujos de trabajo computacionales

### ¿Cómo se organiza un HPC?

- **Nodo de acceso / login o admin**
  - Donde nos conectamos como usuarios (`ssh`)
  - Editamos archivos, preparamos scripts y enviamos procesos o trabajos
  - **No ejecutamos directamente análisis pesados en este nodo**

- **Nodos de cómputo**
  - Donde ejecutamos los trabajos
  - Solo accedemos a estos mediante un scheduler o planificador (Slurm)

- **Sistema de archivos compartido**
  - Los mismos archivos son visibles para todos los nodos
  - Usamos directorios de trabajo temporales

---

## 2. ¿Cómo nos conectamos al clúster?

Desde la terminal local:

```bash
ssh username@123.123.123.123
```

## 3. Archivos de sistema y material del curso

Linux usa un sistema de archivos jerárquico, empezando con la raíz (`/`).

En el curso trabajaremos en ambientes grupales en la siguiente dirección:
```
/data/courses/course2026/nombre_grupo/
```
Los archivos de entrada y scripts se ubicarán dentro de una carpeta común para todos los grupos.
```
/data/courses/course2026/course_files/
```

## 4. Comandos de navegación y manipulación de archivos en Linux

- **pwd**: print working directory
```bash
pwd
```
Nos dice en qué dirección estamos. Para nuestra carpeta de inicio sería (`/data/courses/course2026/nombre_grupo/`).
- **ls**: list files
```bash
ls
ls -l
ls -lh
```
Son comúnes las opciones `l` para imprimir un formato largo y `h` para mostrar tamaños de archivos más entendibles.
- **cd**: change directory
```bash
cd directory name
```
Este puede usarse de varias maneras. Por ejemplo, con una dirección absoluta:
```bash
cd /data/courses/course2026/course_files
```
O una dirección relativa:
```bash
cd ../neltuma # Desde mi posición, ir hacia arriba, y luego a la carpeta neltuma
```
Si me pierdo, puedo regresar a mi directorio de inicio
```bash
cd ~
```
- **mkdir**: make directory
```bash
mkdir 1-hpc_linux 2-genome_qc
```
Hemos creado los directorios para la práctica 1 y 2 (verlos con `ls`)
- **touch**: crear un archivo vacío
```bash
touch file_name
```
Por ejemplo, creemos un archivo vacío dentro de la carepta 1-hpc_linux
```bash
cd 1-hpc_linux
touch saludo.txt
ls -lh
```
- **echo**: imprimir en la terminal
```bash
echo "Hola mundo!"
```
También podemos usar `printf`. Sin embargo, este requiere ser más específico
```bash
printf "Hola mundo!"
printf "Hola mundo!\n"
```
Cualquier impresión en la terminal se puede guardar en un archivo usando una flecha `>`.
```bash
printf "Hola mundo!\n" > saludo.txt
```
Si el archivo no existe, se creará al especificar con la flecha. Si el archivo existe, una flecha reemplazará el contenido del archivo original. Por otro lado, dos flechas `>>` aumentará al contenido original.
```bash
printf "Adiós mundo!\n" >> despedida.txt
printf "Adiós mundo!\n" >> saludo.txt
```
- **cat**: concatenar el contenido de uno o más archivos e imprimir el resultado
```bash
cat despedida.txt
cat saludo.txt
cat despedida.txt saludo.txt
```
El contenido concatenado también se puede guardar o añadir a otro archivo con las flechas.
- **less**: mostrar archivos largos de manera segura
```bash
less ../../course_files/secuencias.fasta
```
Se abre una ventana que podemos explorar usando las flechas del teclado. Podemos controlar el ajuste de línea con la opción `-S`
```bash
less -S ../../course_files/secuencias.fasta
```
Para salir, usamos la tecla `q`
- **ln**: crear un enlace desde una dirección a otra
```bash
ln -s /data/courses/courses2026/course_files/secuencias.fasta .
ls -lh
```
Útil para acortar la longitud de los comandos y facilitar el trabajo en un solo ambiente. Se pueden crear enlaces de archivos y directorios.
```bash
ln -s /data/courses/courses2026/course_files/ .
```
- **cp**: copiar archivos y directorios
```bash
cp saludo.txt saludo_copia.txt
cp course_files/saludo.sh .
```
Para copiar directorios con el contenido, agregamos la opción `-r`
- **mv**: mover archivos y directorios, renombrar archivos
```bash
mv saludo.txt saludo_y_despedida.txt
```
Funciona con un archivo o dirección "desde" a un archivo o dirección "hasta". Si se elige un "hasta" que ya existe, se reemplazará el archivo original, perdiendo su contenido.
- **rm**: eliminar archivos y directorios
```bash
rm saludo_copia.txt
```
Para eliminar directorios con el contenido, agregamos la opción `-r`. Una vez eliminado, el archivo no puede ser recuperado. En lo posible, evitar usar direcciones absolutas para prevenir errores irreversibles.
- **nano**: editar archivos
```bash
nano despedida.txt
```
Si el archivo no existe, se creará una ventana vacía en la cual podemos escribir contenido. Si el archivo existe, se abrirá una ventana con el contenido para editar. Agreguemos lo siguiente:
```bash
#!/bin/bash
echo "Adiós mundo!"
```
Salimos con `^X`, y para guardar el contenido con `^Y` y `ENTER` para mantener el mismo nombre.

## 5. Permisos de lectura, escritura y ejecución

Cada archivo o directorio tiene:
- Dueño
- Grupo
- Permisos

Por ejemplo, usando `ls -l`. Podemos observar algo como:
```bash
drwxr-x--- 2 neltuma genpob2025 4096 saludo_y_despedida.txt
```
Donde `r`, `w` y `x` se refiere a los permisos de lectura, escritura y ejecución, respectivamente. Se muestran en el orden de permisos para el dueño, el grupo y todos los demás.  
Intentemos ejecutar un script de ejemplo. Esto se puede hacer de dos maneras:
```bash
./saludo.sh
bash saludo.sh
```
Podemos cambiar los permisos de ejecución con `chmod`
```bash
chmod +x despedida.txt
./despedida.txt
```
