# Dirty COW 2 : Vulnerabilidad en el linux kernel

**Dirty COW** (CVE-2016-5195) es un exploit que se aprovecha de un error en el kernel de Linux relacionado con la mecánica de **copia sobre escritura** (Copy-On-Write, COW). permite a un atacante local **escalar privilegios** para obtener acceso root.

## ¿Cómo funciona?

1. **COW (Copy-On-Write):**  
   Es una técnica usada para optimizar el uso de memoria. Cuando varios procesos comparten un archivo en memoria, el kernel solo les da acceso de solo lectura. Si uno de ellos intenta modificar el archivo, se crea una copia para evitar alterar el original.

## Uso y compilacion

```bash
   g++ -Wall -pedantic -O2 -std=c++11 -pthread -o dcow 40847.cpp -lutil
```

## Ejecución

```bash
  ./dcow -s
```
## Demostración

![image](https://github.com/user-attachments/assets/61964cf3-0943-4e11-97e7-2561f417f596)

-En este caso lo quise probar con un CTF [stapler](https://www.vulnhub.com/entry/stapler-1,150/) el cual contaba con las condiciones para explotar esta vulnerabilidad en el kernel

```bash
Available information:

Kernel version: 4.4.0
Architecture: i686
Distribution: ubuntu
Distribution version: 16.04
Additional checks (CONFIG_*, sysctl entries, custom Bash commands): performed
Package listing: from current OS

```


2. **El problema:**  
   Dirty COW explota una condición de carrera en la implementación de COW. Si se manipula el archivo compartido de manera precisa (forzando al kernel a copiar datos mientras se escriben directamente en memoria), es posible **escribir datos en un archivo de solo lectura**, como en la ruta `/etc/passwd`.

3. **Qué permite:**  
   - Cambiar el archivo `/etc/passwd` para incluir un usuario con privilegios root.
   - Insertar código malicioso en archivos que deberían ser solo lectura.
   - Escalar privilegios en el sistema.

## ¿Qué tan grave es?

Dirty COW es extremadamente peligroso porque:
- **No requiere permisos especiales.** Cualquier usuario local puede usarlo.
- Funciona en **casi casi todos los kernels de Linux :d** antiguos, especialmente versiones previas a 4.8.3 por que fue parcheada a partir de esta versión.

## Mitigación

- Actualizar el kernel a una versión parcheada.
- Usar herramientas de detección de exploits y reforzar la seguridad del sistema.

