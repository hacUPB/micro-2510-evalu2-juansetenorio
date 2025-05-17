
Máquinas de estados 

Una máquina de estados finitos (o MEF) es un modelo matemático y gráfico para representar sistemas cuyo comportamiento depende de una secuencia de eventos. Sus elementos clave son:

1. Estados: Un conjunto finito de “situaciones” en las que el sistema puede encontrarse.
2. Entradas: Eventos o señales externas que disparan cambios.
3. Transiciones: Reglas que, en función del estado actual y (en el caso de Mealy) de la entrada, determinan el siguiente estado.
4. Salidas: Acciones o señales generadas, que pueden depender solo del estado (Máquina de Moore) o del estado y la entrada (Máquina de Mealy).

Máquina Moore

En una Máquina de Moore, las salidas dependen únicamente del estado actual y no de las entradas en el instante de transición.

Características: 

-Salidas ligadas al estado
    Cada estado lleva asociada una o varias salidas fijas.
    Al entrar en un estado, se activan inmediatamente las salidas correspondientes.
-Transición basada en la entrada
    Las flechas de transición (de un estado a otro) se disparan cuando se cumple una condición sobre la entrada, pero la salida no cambia hasta que entras al nuevo estado.
-Ventajas
    Salidas estables: no “parpadean” si la entrada cambia bruscamente.
    Diseño sencillo cuando la lógica de salida no requiere reacción instantánea a la entrada.
-Inconvenientes
    Puede haber latencia: la respuesta a una entrada ocurre sólo en la frontera de estados, no al instante.

Máquina de Mealy

Una Máquina de Mealy es un tipo de máquina de estados finitos en la que las salidas dependen tanto del estado actual como de las entradas en el momento de la transición.

Características:

-Salidas ligadas a transición
    Cada flecha (transición) va anotada con “condición_entrada / salida”.
    La salida puede cambiar inmediatamente al cumplirse la condición, sin esperar a entrar al siguiente estado.
-Transición basada en estado + entrada
    El siguiente estado se determina como en cualquier FSM, pero la salida se genera “on-the-fly” al evaluar la entrada.
-Ventajas
    Respuesta más rápida: la salida reacciona al instante ante cambios de entrada.
    A menudo reduce el número de estados necesarios.
-Inconvenientes
    Diseño de salidas puede resultar más complejo y menos predecible, porque cambian dentro de los cambios de estado.


Diferencias:

| Característica            | Máquina de Moore                          | Máquina de Mealy                                        |
|---------------------------|-------------------------------------------|---------------------------------------------------------|
| **Dependencia de la salida** | Solo del estado actual                    | Del estado actual **y** de la entrada                   |
| **Lugar de la salida**    | Asociada a cada estado                    | Asociada a cada transición                              |
| **Latencia en la respuesta** | Mayor: la salida cambia al entrar en el siguiente estado | Menor: la salida puede cambiar inmediatamente           |
| **Número de estados**     | Suele ser mayor                           | Suele ser menor                                          |
| **Estabilidad de salidas**| Alta                                      | Menor                                                   |
| **Complejidad de diseño** | Más simple                                | Más complejo                                            |
| **Casos de uso típicos**  | Salidas estables (semáforos, displays)    | Respuesta rápida (controles interactivos, protocolos)   |
