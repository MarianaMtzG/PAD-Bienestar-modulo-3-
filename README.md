#include <stdio.h>
#include <stdlib.h>

// 1. Estructura que representa cada nodo (turno) en la cola
typedef struct Nodo {
    int numeroTurno;        // Almacena el número de turno asignado
    struct Nodo* siguiente; // Apuntador al siguiente nodo en la fila
} Nodo;

// Estructura que controla los extremos de la cola dinámica
typedef struct Cola {
    Nodo* inicio; // Apunta al primer elemento (próximo a ser atendido)
    Nodo* fin;    // Apunta al último elemento (el recién llegado)
} Cola;

// Prototipos de las funciones
void inicializarCola(Cola* q);
int estaVacia(Cola* q);
void agregarTurno(Cola* q, int turno);
void atenderTurno(Cola* q);
void mostrarCola(Cola q);

// Función Principal
int main() {
    Cola miCola;
    inicializarCola(&miCola); // Inicializa los apuntadores en NULL
    
    int opcion, turno;

    do {
        // Menú interactivo para el usuario
        printf("\n=========================================\n");
        printf("   SISTEMA DE TURNOS PAD-BIENESTAR\n");
        printf("=========================================\n");
        printf("1. Agregar un turno (Registrar)\n");
        printf("2. Atender un turno (FIFO)\n");
        printf("3. Mostrar la cola de turnos en espera\n");
        printf("4. Salir del programa\n");
        printf("Seleccione una opcion: ");
        scanf("%d", &opcion);

        switch (opcion) {
            case 1:
                printf("Ingrese el numero de turno que desea registrar: ");
                scanf("%d", &turno);
                agregarTurno(&miCola, turno);
                break;
            case 2:
                atenderTurno(&miCola);
                break;
            case 3:
                mostrarCola(miCola);
                break;
            case 4:
                printf("Cerrando el sistema PAD-Bienestar. ¡Hasta luego!\n");
                break;
            default:
                printf("Opcion no valida. Intente de nuevo.\n");
        }
    } while (opcion != 4);

    // Limpieza de memoria: Liberar nodos restantes si el usuario sale antes de vaciar la cola
    while (!estaVacia(&miCola)) {
        atenderTurno(&miCola);
    }

    return 0;
}

// Inicializa la estructura de la cola apuntando a NULL (cola vacía)
void inicializarCola(Cola* q) {
    q->inicio = NULL;
    q->fin = NULL;
}

// Función auxiliar para validar si la cola carece de elementos
int estaVacia(Cola* q) {
    return (q->inicio == NULL);
}

// Inserta un nuevo turno al final de la cola (Enqueue)
void agregarTurno(Cola* q, int turno) {
    // Asignación dinámica de memoria mediante malloc
    Nodo* nuevoNodo = (Nodo*)malloc(sizeof(Nodo));
    
    // Validación en caso de que el sistema se quede sin memoria RAM
    if (nuevoNodo == NULL) {
        printf("Error de sistema: No se pudo asignar memoria para el turno.\n");
        return;
    }

    // Configuración de los datos del nuevo nodo
    nuevoNodo->numeroTurno = turno;
    nuevoNodo->siguiente = NULL; // Al ser el último, no tiene a nadie detrás

    // Si la cola está vacía, el nuevo nodo es tanto el inicio como el fin
    if (estaVacia(q)) {
        q->inicio = nuevoNodo;
        q->fin = nuevoNodo;
    } else {
        // Si ya hay elementos, el nodo que estaba al final ahora apunta al nuevo
        q->fin->siguiente = nuevoNodo;
        // El apuntador de control 'fin' se actualiza al nuevo nodo
        q->fin = nuevoNodo;
    }
    printf("Exito: Turno %d agregado correctamente a la fila.\n", turno);
}

// Elimina y atiende al usuario al inicio de la cola (Dequeue - FIFO)
void atenderTurno(Cola* q) {
    // Validación del comportamiento cuando la cola se encuentre vacía
    if (estaVacia(q)) {
        printf("Aviso: No hay turnos en espera. La cola esta vacia.\n");
        return;
    }

    // Guardamos la dirección del nodo que vamos a eliminar en un apuntador temporal
    Nodo* temporal = q->inicio;

    printf("Atendiendo en ventanilla al Turno: %d\n", temporal->numeroTurno);

    // Desplazamos el inicio de la cola al siguiente turno
    q->inicio = q->inicio->siguiente;

    // Si al mover el inicio la cola se quedó sin nodos, el puntero fin también debe ir a NULL
    if (q->inicio == NULL) {
        q->fin = NULL;
    }

    // Liberación de la memoria dinámica asignada previamente al nodo atendido
    free(temporal);
}

// Muestra en pantalla el estado actual y orden de los turnos en espera
void mostrarCola(Cola q) {
    // Validación si no hay elementos que mostrar
    if (q.inicio == NULL) {
        printf("La cola actual se encuentra vacia.\n");
        return;
    }

    // Apuntador auxiliar para recorrer la lista sin perder el control de la estructura original
    Nodo* actual = q.inicio;
    
    printf("\n--- ESTADO DE LA FILA DE ESPERA ---\n");
    printf("PROXIMO A ATENDER -> ");
    
    // Ciclo para recorrer e imprimir cada nodo hasta llegar al final de la cola
    while (actual != NULL) {
        printf("[%d] ", actual->numeroTurno);
        actual = actual->siguiente; // Avanza al siguiente nodo
    }
    printf("<- ULTIMO EN LLEGAR\n");
}
    

