#include <stdio.h>
#include <stdlib.h>  
 
// Definición de la estructura para cada nodo de la cola
typedef struct Nodo {
    int numeroTurno;       // Dato que almacena el número de turno
    struct Nodo* siguiente; // Apuntador al siguiente nodo en la cola
} Nodo;

// Estructura que controla la cola (apuntadores al inicio y al final)
typedef struct Cola {
    Nodo* inicio;
    Nodo* fin;
} Cola;

// Funciones para la gestión de la cola
void inicializarCola(Cola* q);
int estaVacia(Cola* q);
void agregarTurno(Cola* q, int turno);
void atenderTurno(Cola* q);
void mostrarCola(Cola q); 

// Función principal
int main() {
    Cola miCola; 
    inicializarCola(&miCola); // Se inicializan los apuntadores en NULL
    
    int opcion, turno;

    do {
        printf("\n=== SISTEMA DE TURNOS PAD-BIENESTAR ===\n");
        printf("1. Agregar un turno\n");
        printf("2. Atender un turno\n");
        printf("3. Mostrar la cola de turnos\n");
        printf("4. Salir\n");
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
                printf("Saliendo del sistema. ¡Que tenga un buen dia!\n");
                break;
            default:
                printf("Opcion invalida. Intente de nuevo.\n");
        }
    } while (opcion != 4);

    // Liberar memoria restante antes de salir si la cola no quedó vacía
    while (!estaVacia(&miCola)) {
        atenderTurno(&miCola);
    }

    return 0;
}

// Inicializa la cola configurando los apuntadores de inicio y fin como vacíos (NULL)
void inicializarCola(Cola* q) {
    q->inicio = NULL;
    q->fin = NULL;
}

// Valida si la cola se encuentra vacía
int estaVacia(Cola* q) {
    return (q->inicio == NULL);
}

// Inserta un nuevo turno al final de la cola (Enqueue)
void agregarTurno(Cola* q, int turno) {
    // Asignación dinámica de memoria para el nuevo nodo
    Nodo* nuevoNodo = (Nodo*)malloc(sizeof(Nodo));
    
    // Validar si la memoria se asignó correctamente
    if (nuevoNodo == NULL) {
        printf("Error: No hay memoria suficiente para registrar el turno.\n");
        return;
    }

    // Asignar los valores al nuevo nodo
    nuevoNodo->numeroTurno = turno;
    nuevoNodo->siguiente = NULL;

    // Si la cola está vacía, el nuevo nodo es tanto el inicio como el fin
    if (estaVacia(q)) {
        q->inicio = nuevoNodo;
        q->fin = nuevoNodo;
    } else {
        // Si ya hay nodos, el último apunta al nuevo, y el fin se actualiza
        q->fin->siguiente = nuevoNodo;
        q->fin = nuevoNodo;
    }
    printf("Turno %d registrado exitosamente.\n", turno);
}

// Elimina y atiende el primer turno de la cola (Dequeue - Principio FIFO)
void atenderTurno(Cola* q) {
    // Validación: Comportamiento cuando la cola está vacía
    if (estaVacia(q)) {
        printf("No hay turnos pendientes en la cola. El sistema esta vacio.\n");
        return;
    }

    // Apuntador temporal para no perder la referencia del nodo a eliminar
    Nodo* temporal = q->inicio;

    printf("Atendiendo al turno: %d\n", temporal->numeroTurno);

    // Desplazar el inicio de la cola al siguiente nodo
    q->inicio = q->inicio->siguiente;

    // Si al mover el inicio la cola queda vacía, el fin también debe ser NULL
    if (q->inicio == NULL) {
        q->fin = NULL;
    }

    // Liberar de manera segura la memoria dinámica del nodo atendido
    free(temporal);
}

// Recorre la cola de principio a fin para mostrar el estado actual
void mostrarCola(Cola q) {
    // Validación: Comportamiento cuando la cola está vacía
    if (q.inicio == NULL) {
        printf("La cola de turnos esta vacia actualmente.\n");
        return;
    }

    Nodo* actual = q.inicio;
    printf("\n--- ESTADO ACTUAL DE LA COLA ---\n");
    printf("Inicio -> ");
    
    // Bucle para recorrer cada nodo dinámico hasta llegar al final (NULL)
    while (actual != NULL) {
        printf("[%d] ", actual->numeroTurno);
        actual = actual->siguiente;
        
    }
    printf("<- Fin\n");
}
