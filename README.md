# Navia_Anamaria_Gest_Memoria
# Gestion_de_Memoria
https://github.com/a-navia/Gest_Memoria.git
### Dependecia
WSL https://www.microsoft.com/store/productId/9PDXGNCFSCZV?ocid=pdpshare
Tener version windows actualizada.
1. instalacion wsl para hacer uso de mmap y wait:
   -lanzar powershell como admin :
   - wsl --install
   - lanza ubuntu:
   - ingrese un nombre de usuario y una contraseña.
   - sudo apt update && sudo apt upgrade -y \n sudo apt install build-essential gdb.
   - sudo apt install git.
   - abrir en cilion toolchain y añadir wsl y configurar.
     
### Explicación paso a paso:
objetivo: entender el proceso de memoria, cambiar de windows a linux mediantes varios metodos.


'   1. Primero, importamos las bibliotecas necesarias para la asignación de memoria y la creación de procesos.

'
   2. Creamos una constante SIZE para determinar el tamaño de la región de memoria compartida que queremos asignar.

'
    3.En la función principal, llamamos a la función mmap() para asignar una región de memoria que pueda ser compartida entre procesos. Los argumentos de esta función son los siguientes:
'       
'
        NULL: Le estamos pidiendo al sistema operativo que elija la dirección de inicio de la región de memoria.
        SIZE: Es el tamaño de la región que queremos asignar.
        PROT_READ|PROT_WRITE: Estamos indicando que la región de memoria puede ser leída y escrita.
        MAP_SHARED|MAP_ANONYMOUS: Estas banderas indican que la región de memoria será compartida entre procesos y que no estará respaldada por ningún archivo.
        -1: Este argumento sería el descriptor de archivo si la memoria estuviera respaldada por un archivo, pero como usamos MAP_ANONYMOUS, podemos poner -1.
        0: Este sería el desplazamiento si la memoria estuviera respaldada por un archivo. Como no es el caso, ponemos 0.

'
    4. Comprobamos si la asignación fue exitosa. Si mmap() devuelve MAP_FAILED, significa que hubo un error.

'
    5. Creamos un nuevo proceso utilizando la función fork(). Esta función devuelve el PID del proceso hijo al proceso padre y 0 al proceso hijo.

'
    6. Luego, comprobamos el valor devuelto por fork(). Si es menor que 0, hubo un error al crear el nuevo proceso.

'
    7. Si el valor devuelto es 0, significa que estamos en el proceso hijo. En este caso, leemos y mostramos la memoria compartida y luego liberamos la memoria utilizando la función munmap().

'
    8. Si el valor devuelto por fork() es mayor que 0, significa que estamos en el proceso padre. En este caso, escribimos en la memoria compartida, esperamos a que el proceso hijo termine y luego liberamos la memoria.
```
#include <sys/mman.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

#define SIZE 4096

int main() {
    char *shared_memory = mmap(NULL, SIZE, PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);
    if (shared_memory == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }

    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        exit(EXIT_FAILURE);
    } else if (pid == 0) {
        printf("Child reads: %s\n", shared_memory);
        munmap(shared_memory, SIZE);
        exit(EXIT_SUCCESS);
    } else {
        strcpy(shared_memory, "Hello, child process!");
        wait(NULL);
        munmap(shared_memory, SIZE);
    }

    return 0;
}
```   
Espero que esta explicación te ayude a entender mejor el código. Si tienes más preguntas, no dudes en hacerlas.
