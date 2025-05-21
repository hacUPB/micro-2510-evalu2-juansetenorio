ACTIVIDAD 2 


1. Lenguaje C en sistemas embebidos
Definición:
    C es un lenguaje de programación de propósito general que ofrece un equilibrio entre abstracción (sintaxis relativamente legible) y control de bajo nivel (acceso a direcciones de memoria y registros de hardware). Por ello es muy popular en sistemas embebidos, donde los recursos (CPU, RAM, almacenamiento) son limitados y se requiere eficiencia.

2. Ventajas de programar en C
    Código compacto y claro: La sintaxis de C permite expresar algoritmos complejos con pocas líneas.
    Mantenimiento sencillo: Separación de interfaz (cabeceras) y implementación (código fuente).
    Trabajo en equipo: Facilita la organización de proyectos grandes con múltiples módulos.
    Portabilidad: Con pequeños ajustes de compilador puede correr en distintos microcontroladores.
3. Desventajas de programar en C
    Costo de compilador: Las toolchains industriales pueden ser caras.
    Eficiencia en casos extremos: Para rutinas muy críticas a veces el ensamblador puro genera binarios aún más optimizados.
4. Estructura básica de un programa en C
    Directivas de preprocesador (#include, #define): Instrucciones que el compilador expande o sustituye antes de compilar.
    Prototipos de funciones: Declaraciones que informan al compilador sobre funciones usadas en el programa.
    Función main(): Punto de entrada donde inicia la ejecución.
    Definición de funciones auxiliares: Código modularizado que realiza tareas específicas.
5. Macros y preprocesador
Definición:
    Fragmentos de código o constantes simbólicas que el preprocesador sustituye literalmente antes de la compilación.
    Macros con parámetros (p. ej. #define SQUARE(x) ((x)*(x))) permiten generar código inline sin sobrecarga de llamada a función.
    Macros de mapeo de hardware usan punteros volatile para acceder a registros en direcciones fijas.
6. Tipos de datos en C
Básicos:
    char (carácter, 1 byte)
    int (entero, típicamente 2–4 bytes)
    float (real simple precisión, 4 bytes)
    double (real doble precisión, 8 bytes)
    Enteros con tamaño fijo (<stdint.h>): uint8_t, int16_t, uint32_t, etc., garantizando ancho de bit.
7. Operadores
    Aritméticos: +, -, *, /, %
    Lógicos: && (AND), || (OR), ! (NOT)
A nivel de bits:
    AND (&), OR (|), XOR (^), NOT (~)
    Desplazamientos: izquierda (<<), derecha (>>)
8. Control de flujo
    Estructuras que dirigen la ejecución:
    Condicionales: if, else if, else, switch
    Bucles: for, while, do … while
9. Punteros y variables volatile
    Punteros: Variables que almacenan direcciones de memoria, esenciales para acceso directo a registros de hardware.
    volatile: Calificador que impide optimizaciones en variables cuyo valor puede cambiar “por fuera” del flujo normal (p. ej. en una ISR o dispositivo).
10. Funciones y recursión
    Funciones: Bloques de código con nombre, que reciben parámetros y opcionalmente devuelven valores, para modularizar.
    Recursión: Una función que se llama a sí misma; requiere un caso base para evitar bucle infinito y desbordamiento de pila.
11. Estructuras (struct) y uniones (union)
    struct: Agrupa múltiples campos de distintos tipos bajo un mismo nombre, representando datos complejos.
    union: Define varios campos que comparten la misma dirección de memoria; útil para reinterpretar bit a bit un dato (por ejemplo, ver los bytes de un float).




