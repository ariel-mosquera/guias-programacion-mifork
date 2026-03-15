<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

En lenguajes como C, donde no existe un mecanismo nativo de excepciones, la gestión de errores recae en la comunicación explícita entre la función que detecta el problema y la función que la ha invocado. Dado que se requiere que el error sea informado fuera de la función matemática, esta última debe emplear su valor de retorno o sus parámetros para señalizar que ha ocurrido una anomalía, permitiendo al código invocador reaccionar adecuadamente.

La primera opción consiste en utilizar un "valor centinela". Si el rango de resultados válidos de una función no abarca todos los valores posibles de su tipo de retorno, se puede reservar un valor específico para indicar un error. En el caso de una raíz cuadrada, el resultado en el dominio de los números reales siempre es positivo o cero. Por lo tanto, se puede devolver un número negativo (como `-1.0`) para advertir que el argumento proporcionado era inválido.

```c
#include <stdio.h>
#include <math.h>

// Opción 1: Retorno de un valor centinela
float calcular_raiz(float numero) {
    if (numero < 0.0f) {
        return -1.0f; // Valor que indica error
    }
    return sqrt(numero);
}

int main() {
    float num = -4.0f;
    float resultado = calcular_raiz(num);

    if (resultado < 0.0f) {
        printf("Error: No se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("La raiz es: %f\n", resultado);
    }
    return 0;
}

```

La segunda opción se basa en separar el estado de la operación de su resultado matemático. Para ello, la función devuelve un código de estado (por ejemplo, un número entero donde `1` significa éxito y `0` significa error) y el resultado de la operación matemática se exporta a través de un parámetro por referencia utilizando punteros. De esta forma, el código llamador primero verifica el código de retorno y, solo si este indica éxito, procede a utilizar la variable pasada por referencia.

```c
#include <stdio.h>
#include <math.h>

// Opción 2: Código de retorno y parámetro por referencia
int calcular_raiz_ptr(float numero, float *resultado) {
    if (numero < 0.0f) {
        return 0; // Código de error
    }
    *resultado = sqrt(numero);
    return 1; // Código de éxito
}

int main() {
    float num = -4.0f;
    float resultado;

    // Se evalúa el estado de la operación directamente en el if
    if (calcular_raiz_ptr(num, &resultado) == 0) {
        printf("Error: No se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("La raiz es: %f\n", resultado);
    }
    return 0;
}

```

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Concepto y Objetivos de las Excepciones

Una **excepción** se define formalmente como un evento anómalo o inesperado que ocurre durante la ejecución de un programa y que interrumpe el flujo normal de las instrucciones. Considerando los conocimientos previos sobre clases y objetos, en lenguajes como Java una excepción no es un simple número entero, sino que es un **objeto** instanciado a partir de una clase específica. Este objeto empaqueta información valiosa sobre el error, como el tipo de fallo ocurrido, un mensaje descriptivo y la secuencia de funciones que se estaban ejecutando en ese momento.

Al implementar una función o método, el objetivo principal de utilizar excepciones es **delegar la responsabilidad** del manejo de errores. Si una rutina detecta una situación que le impide continuar y que no puede resolver internamente (como calcular la raíz de un número negativo), interrumpe su ejecución inmediatamente y "lanza" (_throw_) una excepción hacia el exterior. De este modo, la función se vuelve más cohesiva: se enfoca exclusivamente en su lógica principal y avisa al sistema de que algo falló, eliminando la necesidad de inventar valores centinelas o usar punteros solo para indicar el estado, como ocurría en C.

Por otro lado, al llamar a funciones que son susceptibles de fallar, el programador emplea este mecanismo para **separar la lógica normal del manejo de errores**. El sistema de excepciones permite agrupar el "camino feliz" (el código que se ejecuta cuando todo va bien) en un bloque estructurado, mientras que la gestión de los fallos se concentra en bloques dedicados a "capturar" (_catch_) dichos errores. Esto evita que el código principal se llene de múltiples sentencias condicionales (`if`) verificando códigos de retorno después de cada llamada, resultando en programas mucho más limpios, legibles y fáciles de mantener.

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

Al trasladar el problema a Java mediante el paradigma orientado a objetos, la operación matemática se encapsula dentro de los métodos de una clase, en este caso `Calculadora`. A diferencia de C, donde el tipo de retorno se veía modificado o se requerían parámetros por referencia para indicar un fallo, en Java el método conserva su firma natural devolviendo directamente un `double`. Si se detecta una anomalía, como recibir un argumento negativo, se interrumpe el flujo normal instanciando y "lanzando" (_throwing_) un objeto que representa la excepción; habitualmente se emplea la clase `IllegalArgumentException` provista por la biblioteca estándar para estos casos.

```java
public class Calculadora {
    // El método mantiene su firma limpia y devuelve solo el resultado matemático
    public double calcularRaiz(double numero) {
        if (numero < 0.0) {
            // Se instancia y lanza un objeto de excepción con un mensaje descriptivo
            throw new IllegalArgumentException("No se puede calcular la raiz de un numero negativo.");
        }
        return Math.sqrt(numero);
    }
}

```

Para controlar este error desde la rutina invocadora (el método `main`), se debe envolver la llamada conflictiva dentro de una estructura `try-catch`. En el bloque `try` se ubica el "camino feliz", es decir, la secuencia de instrucciones que asume que todo funcionará correctamente. Si el método `calcularRaiz` ejecuta la instrucción `throw`, el bloque `try` se aborta de manera inmediata, omitiéndose cualquier operación matemática o de impresión de resultados que hubiera a continuación.

```java
public class Principal {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        double num = -4.0;

        try {
            // Flujo normal: se intenta ejecutar la operación
            double resultado = calc.calcularRaiz(num);
            System.out.println("La raiz es: " + resultado);

        } catch (IllegalArgumentException e) {
            // Flujo alternativo: se captura y procesa el error
            System.out.println("Error detectado: " + e.getMessage());
        }

        System.out.println("El programa continua su ejecucion normalmente.");
    }
}

```

El bloque `catch` opera como el mecanismo de contención o manejador del error. Cuando la excepción es lanzada desde la clase `Calculadora`, es interceptada por este bloque, el cual recibe como parámetro el propio objeto excepción que fue creado en el momento del fallo. A través de este objeto (referenciado como `e` en el ejemplo), se puede invocar su comportamiento encapsulado, como el método `getMessage()`, extrayendo la información del error para notificar al usuario de forma segura y permitiendo que el programa siga ejecutándose sin "caerse".

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Terminología y Flujo de Ejecución de las Excepciones

**"Lanzar"** (_throw_) una excepción significa detectar una condición de error, instanciar un objeto que encapsule los detalles de dicha anomalía y detener inmediatamente la ejecución del método actual para delegar el problema al sistema. Por el contrario, **"controlar"** o **"capturar"** (_catch_) consiste en establecer un perímetro de seguridad, envolviendo el código propenso a fallos en un bloque `try` e interceptando el objeto de error en un bloque `catch`. En el escenario de la `Calculadora`, el método que realiza la operación matemática "lanza" la excepción al recibir un valor negativo, mientras que el método invocador la "captura" para gestionar el mensaje de aviso, impidiendo la terminación abrupta de la aplicación.

La **"propagación"** de una excepción sucede cuando el método donde se origina el error (o por donde transita) decide no capturarlo internamente. Al no existir un bloque `try-catch` local, el entorno de ejecución de Java transfiere la excepción hacia atrás en la pila de llamadas (_call stack_), entregándosela al método que había invocado a la rutina fallida. Este proceso se repite en cascada, buscando hacia abajo en la pila hasta encontrar una función que sí provea el bloque `catch` adecuado para manejar el error; si se llega al inicio del programa sin controlarla, la aplicación finalmente "craseha" y se detiene por completo.

Un aspecto crítico del mecanismo de excepciones es que la terminación de las funciones durante la propagación es definitiva e irrevocable. A medida que la excepción va escalando por la pila de llamadas, las funciones que no la controlan son abortadas inmediatamente, liberando sus variables locales y saltándose cualquier instrucción restante. **Las funciones no se reanudan** una vez que el error pasa a través de ellas; el flujo de ejecución continúa exclusivamente desde el bloque `catch` que finalmente interceptó el fallo.

Para observar este comportamiento de propagación y terminación definitiva, se puede añadir un método intermedio al ejemplo anterior:

```java
public class Calculadora {
    public double calcularRaiz(double numero) {
        if (numero < 0.0) {
            throw new IllegalArgumentException("Raiz de un numero negativo.");
        }
        return Math.sqrt(numero);
    }
}

public class Principal {
    // Este método actúa como intermediario. No captura la excepción, solo se propaga a través de él.
    public static void realizarOperacion(Calculadora calc, double num) {
        double res = calc.calcularRaiz(num);
        // Si calcularRaiz lanza excepción, esta línea JAMÁS se ejecutará. El método no se reanuda.
        System.out.println("Operacion finalizada dentro del intermediario. Resultado: " + res);
    }

    public static void main(String[] args) {
        Calculadora calc = new Calculadora();

        try {
            // El error ocurre en calcularRaiz, se propaga por realizarOperacion y se captura aquí en main
            realizarOperacion(calc, -4.0);
            System.out.println("Linea despues de llamar al intermediario.");

        } catch (IllegalArgumentException e) {
            System.out.println("Excepcion capturada en main: " + e.getMessage());
        }
    }
}

```

---

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (_stack_) de llamadas?

En lenguajes como C, la propagación de un error a través de múltiples capas de funciones debe realizarse de forma completamente manual. Si una función profunda en la jerarquía de llamadas falla, debe retornar un código de error a su llamador directo, el cual debe verificar dicho código mediante sentencias condicionales (`if`) y, a su vez, retornar otro código a su propio llamador. Este proceso interfiere constantemente con la lógica principal del programa, generando un código verboso y acoplado donde el manejo de errores oscurece el propósito real de las funciones intermedias.

Por el contrario, la **propagación natural** de las excepciones automatiza este proceso, delegando al entorno de ejecución la tarea de escalar el error a través de la pila de llamadas. Cuando ocurre una anomalía, el sistema busca hacia atrás en la jerarquía de funciones activas hasta encontrar un bloque `catch` adecuado. Esto exime a las funciones intermedias de la responsabilidad de verificar, interpretar y redirigir constantemente los estados de error, permitiendo que mantengan sus firmas limpias y se concentren exclusivamente en su cometido original.

Las principales ventajas de este modelo radican en la **legibilidad** y la **seguridad**. Por un lado, se logra una separación nítida entre el flujo normal de las operaciones (el "camino feliz") y el código de gestión de fallos, resultando en un diseño de software mucho más estructurado. Por otro lado, se incrementa drásticamente la robustez del sistema al evitar que los errores sean ignorados por omisión; en C, olvidar la comprobación de un valor de retorno permite que el programa continúe ejecutándose en un estado corrupto o impredecible, mientras que una excepción no gestionada garantiza la interrupción del flujo, forzando a que el problema sea atendido de manera consciente.

---

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?
