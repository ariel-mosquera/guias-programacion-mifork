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

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (_stack_) de llamadas?

En lenguajes como C, la propagación de un error a través de múltiples capas de funciones debe realizarse de forma completamente manual. Si una función profunda en la jerarquía de llamadas falla, debe retornar un código de error a su llamador directo, el cual debe verificar dicho código mediante sentencias condicionales (`if`) y, a su vez, retornar otro código a su propio llamador. Este proceso interfiere constantemente con la lógica principal del programa, generando un código verboso y acoplado donde el manejo de errores oscurece el propósito real de las funciones intermedias.

Por el contrario, la **propagación natural** de las excepciones automatiza este proceso, delegando al entorno de ejecución la tarea de escalar el error a través de la pila de llamadas. Cuando ocurre una anomalía, el sistema busca hacia atrás en la jerarquía de funciones activas hasta encontrar un bloque `catch` adecuado. Esto exime a las funciones intermedias de la responsabilidad de verificar, interpretar y redirigir constantemente los estados de error, permitiendo que mantengan sus firmas limpias y se concentren exclusivamente en su cometido original.

Las principales ventajas de este modelo radican en la **legibilidad** y la **seguridad**. Por un lado, se logra una separación nítida entre el flujo normal de las operaciones (el "camino feliz") y el código de gestión de fallos, resultando en un diseño de software mucho más estructurado. Por otro lado, se incrementa drásticamente la robustez del sistema al evitar que los errores sean ignorados por omisión; en C, olvidar la comprobación de un valor de retorno permite que el programa continúe ejecutándose en un estado corrupto o impredecible, mientras que una excepción no gestionada garantiza la interrupción del flujo, forzando a que el problema sea atendido de manera consciente.

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

En la programación orientada a objetos, las excepciones son efectivamente objetos instanciados a partir de clases especializadas. Cuando se detecta una condición anómala, se construye una instancia que representa dicho error específico y se lanza al flujo de ejecución. Esto supone una evolución significativa respecto a lenguajes estructurados como C, donde los fallos suelen reportarse mediante tipos primitivos, como simples números enteros, los cuales carecen de la capacidad para transportar información adicional de manera unificada y coherente.

Modelar las excepciones como objetos ofrece enormes ventajas en términos de encapsulación. Una excepción actúa como un contenedor seguro que agrupa todo el estado relevante del error: un mensaje descriptivo, la secuencia exacta de llamadas a funciones donde ocurrió (_stack trace_) y la causa subyacente. A través de métodos públicos (como `getMessage()`), el bloque `catch` que intercepta este objeto puede consultarlo para extraer los detalles necesarios de forma controlada, ocultando al programador la complejidad interna de cómo se generó y estructuró dicha información diagnóstica.

Gracias a esta arquitectura basada en clases y objetos, es completamente factible y muy habitual crear excepciones personalizadas. Aplicando los principios de la orientación a objetos, se puede definir una nueva clase que herede de las excepciones estándar proporcionadas por Java (como `Exception` o `RuntimeException`). Esto permite modelar tipos de error propios del dominio del problema, añadiendo atributos privados y métodos específicos que resulten útiles para entender o registrar el fallo con mayor precisión, como se muestra en el siguiente código:

```java
// Creación de una excepción personalizada encapsulando datos adicionales
public class NumeroNegativoException extends RuntimeException {

    // Atributo privado que guarda el valor específico que causó el error
    private double valorInvalido;

    public NumeroNegativoException(String mensaje, double valor) {
        super(mensaje); // Se inicializa el mensaje en la clase base
        this.valorInvalido = valor;
    }

    // Método accesor para que el exterior consulte el estado encapsulado de forma segura
    public double getValorInvalido() {
        return this.valorInvalido;
    }
}

```

Para integrar la excepción personalizada en la clase `Calculadora`, se debe modificar el método que realiza la operación matemática. En lugar de utilizar una clase genérica de la biblioteca estándar de Java, se instancia el nuevo objeto `NumeroNegativoException` cuando se detecta el valor anómalo. Al construir este objeto, se le proporciona tanto el mensaje de texto descriptivo como el valor numérico exacto que provocó el fallo, encapsulando así todo el contexto del error en una única entidad.

```java
public class Calculadora {
    public double calcularRaiz(double numero) {
        if (numero < 0.0) {
            // Se lanza la excepción personalizada enviando el mensaje y el valor erróneo
            throw new NumeroNegativoException("Raiz de un numero negativo no permitida.", numero);
        }
        return Math.sqrt(numero);
    }
}

```

Por su parte, la rutina principal que invoca este código debe adaptar su perímetro de seguridad. El bloque `catch` se configura ahora para interceptar específicamente el tipo `NumeroNegativoException`. Una vez que el objeto de excepción es capturado, se tiene acceso pleno a su interfaz pública, lo que permite invocar el método `getValorInvalido()` para recuperar el dato problemático original y tomar decisiones programáticas basadas en él.

```java
public class Principal {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        double num = -4.0;

        try {
            double resultado = calc.calcularRaiz(num);
            System.out.println("La raiz es: " + resultado);

        } catch (NumeroNegativoException e) {
            // Se extrae la información encapsulada en el objeto excepción
            System.out.println("Error detectado: " + e.getMessage());
            System.out.println("El valor introducido que causo el fallo fue: " + e.getValorInvalido());
        }
    }
}

```

Esta aproximación evidencia una clara ventaja del uso de objetos y la encapsulación para el manejo de errores. En lugar de obligar al programador a analizar o extraer información de una simple cadena de texto genérica, el bloque `catch` puede consultar directamente los atributos tipados del objeto excepción. Así, el código que gestiona el fallo resulta mucho más limpio, robusto y coherente con el paradigma orientado a objetos.

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

Al comparar el manejo de errores en C con el enfoque orientado a objetos de Java, la principal diferencia radica en el contexto que acompaña al fallo. En lenguajes estructurados como C, un error suele notificarse mediante un simple valor numérico (como `-1` o `0`), el cual únicamente señala que la operación ha fracasado, pero carece de detalles adicionales. Por el contrario, la encapsulación en Java permite que cualquier excepción sea un objeto, diseñado para agrupar y transportar simultáneamente múltiples piezas de información diagnóstica desde el punto de origen del error hasta el manejador que finalmente lo captura.

La primera información esencial que aporta un objeto excepción es su propio **tipo** o clase (por ejemplo, `IllegalArgumentException` o `NumeroNegativoException`). Este tipo permite que la estructura de control de errores decida de manera automática qué bloque `catch` debe activarse para procesar ese fallo concreto. Acompañando a la clase del objeto, siempre se encapsula un **mensaje descriptivo** en formato de cadena de texto. Este mensaje, accesible a través de métodos como `getMessage()`, explica el motivo humano y técnico de la anomalía, eliminando la necesidad de mantener manuales externos para descifrar qué significa un código de error numérico abstracto.

La información más poderosa y distintiva que transporta todo objeto excepción es la **traza de la pila de llamadas** (_stack trace_). Consiste en un registro generado de forma automática por el entorno de ejecución que detalla la secuencia exacta de llamadas a funciones que condujeron al fallo, incluyendo el archivo, la clase, el método y el número de línea específicos donde se originó el problema. Cuando un manejador intercepta el objeto, tiene la capacidad de consultar o imprimir esta traza (generalmente con el método `printStackTrace()`), proporcionando un mapa exhaustivo para diagnosticar el flujo de ejecución defectuoso de manera inmediata, una tarea que en C requeriría herramientas de depuración externas o la impresión manual de múltiples variables en cada función.

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta gemini:

En Java, es completamente lícito asociar múltiples bloques `catch` a un único bloque `try`. Esta estructura se diseña cuando el código protegido es susceptible de generar diferentes tipos de anomalías y se requiere aplicar una estrategia de recuperación o de notificación distinta para cada una de ellas. Actúa de manera análoga a una estructura `switch` o a una cadena de condicionales `if-else if` en C, donde cada bloque actúa como un filtro especializado en interceptar una clase concreta de objeto excepción.

A pesar de poder definir una amplia lista de manejadores, **solamente se ejecuta un bloque `catch` como máximo** por cada excepción lanzada. Cuando se interrumpe el bloque `try` debido a un error, el entorno de ejecución evalúa los bloques `catch` de manera estricta y secuencial, de arriba hacia abajo. El sistema seleccionará el primer bloque cuyo parámetro coincida exactamente con el tipo del objeto excepción lanzado, o bien que sea una clase "padre" (superclase) del mismo. Una vez que el bloque seleccionado finaliza su tarea, el flujo del programa omite automáticamente el resto de los bloques `catch` y continúa con las instrucciones ubicadas después de toda la estructura.

Debido a este mecanismo de evaluación secuencial, el orden en el que se declaran estos bloques es un factor crítico en el diseño. Es obligatorio colocar siempre las excepciones más específicas (las subclases en la jerarquía) en la parte superior, y las más genéricas (las superclases) en la parte inferior. Si se ubicara un manejador genérico al principio, este capturaría todos los objetos de error indiscriminadamente, haciendo que los bloques inferiores fuesen inalcanzables, situación que el propio compilador de Java detecta y marca como un error de compilación.

```java
try {
    // Código que podría lanzar múltiples tipos de excepciones
    double resultado = calc.calcularRaiz(num);

} catch (NumeroNegativoException e) {
    // 1º filtro: Captura la excepción específica y más detallada
    System.out.println("Error matemático: " + e.getValorInvalido());

} catch (RuntimeException e) {
    // 2º filtro: Captura cualquier otro error de ejecución no previsto arriba
    System.out.println("Error genérico de ejecución: " + e.getMessage());
}

```

Es afirmativo. Desde la versión 7 de Java, existe una característica conocida como "captura múltiple" o _multi-catch_, que permite agrupar varias excepciones en un único bloque `catch`. Para ello, se emplea el símbolo `|`, el cual se reconoce en C y C++ como el operador lógico OR a nivel de bits (_bitwise OR_), pero que en este contexto gramatical actúa como un operador de unión de tipos. El propósito de esta sintaxis es reducir la duplicación de código cuando diferentes anomalías requieren exactamente la misma lógica de tratamiento o de registro.

Al utilizar esta estructura, es necesario cumplir con una regla semántica estricta impuesta por el compilador: las clases de excepciones agrupadas mediante el operador `|` no pueden mantener una relación de herencia directa entre sí. Por consiguiente, no está permitido capturar simultáneamente una clase base y su clase derivada en la misma declaración (por ejemplo, `NumeroNegativoException | RuntimeException`). Si se intentara, el compilador emitiría un error, puesto que el manejador de la superclase ya abarca implícitamente a la subclase, haciendo que la inclusión de esta última resulte redundante.

Adicionalmente, existe una particularidad técnica en la captura múltiple respecto al manejo del objeto de error. Cuando se agrupan varias excepciones en un mismo `catch`, la variable de referencia (típicamente denominada `e`) se declara de forma implícita como constante (`final`). Esto implica que, dentro del alcance del manejador, está prohibido reasignar dicha variable para que apunte a un objeto diferente, asegurando que la información original del error permanezca inalterada durante su procesamiento.

```java
try {
    // Código susceptible de lanzar diferentes tipos de excepciones sin relación de herencia
    double resultado = calc.calcularRaiz(num);
    int division = 10 / (int) num; // Podría lanzar ArithmeticException si num es 0

} catch (NumeroNegativoException | ArithmeticException e) {
    // Un único bloque gestiona ambos fallos utilizando la misma rutina
    System.out.println("Se ha interrumpido la ejecucion por un error en los calculos.");
    System.out.println("Detalle tecnico: " + e.getMessage());

    // e = new RuntimeException(); // Esto causaría un error de compilación por ser 'e' implícitamente final
}

```

### Mi respuesta:

Sí, puede haber más de un bloque `catch` para un mismo bloque `try`, o bien un mismo bloque `catch` puede tener mas de una excepeción como condición de ejecución haciendo uso del operador lógico **_or_** binario, que seria una unica barra vertical "`|`"

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

Cuando una excepción interrumpe el flujo normal de ejecución, se corre el riesgo de abandonar de forma abrupta recursos adquiridos previamente, como archivos abiertos, conexiones de red o memoria dinámica. En lenguajes como C, esto obligaría a duplicar el código de limpieza (cerrar ficheros, liberar punteros) antes de cada posible instrucción `return` o punto de salida por error. Para solucionar este problema de forma centralizada y segura, Java proporciona el bloque `finally`. Este bloque se asocia a una estructura `try` y ofrece una garantía absoluta: su contenido se ejecutará invariablemente al finalizar el bloque `try` o el bloque `catch`, ocurra o no un fallo, e incluso si hay una instrucción `return` dentro de ellos.

La configuración más habitual es el uso conjunto de `try-catch-finally`. En este escenario, si se detecta una anomalía, el flujo se desvía al manejador `catch` para registrar o mitigar el error y, justo después, ingresa al bloque `finally` para realizar las tareas de clausura del recurso. Una vez finalizada la limpieza, el programa continúa su ejecución con normalidad, garantizando que el entorno queda en un estado consistente.

```java
public static void procesarConCatch() {
    System.out.println("1. Abriendo un archivo imaginario...");
    try {
        System.out.println("2. Leyendo datos del archivo...");
        // Simulamos un error durante la lectura lanzando una excepción
        throw new RuntimeException("Error de lectura");

    } catch (RuntimeException e) {
        System.out.println("3. Capturando error: " + e.getMessage());

    } finally {
        // Esta sección se ejecuta SIEMPRE, garantizando el cierre del recurso
        System.out.println("4. Cerrando el archivo imaginario en finally.");
    }
    System.out.println("5. Fin de la funcion. El programa se recuperó y continua.");
}

```

Por otro lado, es perfectamente válido omitir el bloque `catch` y utilizar únicamente la estructura `try-finally`. Este diseño es sumamente útil cuando una función adquiere un recurso que requiere liberación local, pero la función no tiene la responsabilidad (o el contexto adecuado) para solucionar el error, debiendo delegarlo a su llamador. Al ocurrir la anomalía, el bloque `try` se aborta y se ejecuta inmediatamente el bloque `finally` para limpiar; una vez concluida esta tarea crítica, la excepción continúa su propagación natural hacia arriba en la pila de llamadas.

```java
public static void procesarSinCatch() {
    System.out.println("1. Abriendo otro archivo imaginario...");
    try {
        System.out.println("2. Procesando datos...");
        // Simulamos un error insalvable a este nivel
        throw new RuntimeException("Fallo critico irrecuperable aqui");

    } finally {
        // El recurso se libera de forma segura antes de que el error escape
        System.out.println("3. Cerrando el archivo en finally ANTES de propagar el error.");
    }
    // La siguiente línea JAMÁS se ejecutará, porque la excepción se está propagando
    // hacia la función que haya invocado a procesarSinCatch().
}

```

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

En Java, es completamente válido estructurar un bloque `try` seguido únicamente por un bloque `finally`, omitiendo por completo los manejadores `catch`. Como se introdujo previamente, esta configuración se emplea cuando la función actual necesita asegurar la liberación de recursos locales (como cerrar un archivo o una conexión de red) pero no tiene la capacidad o la responsabilidad de gestionar el error detectado. De este modo, se garantiza el cierre de los recursos antes de que la excepción se propague de forma natural hacia la función llamadora.

La característica fundamental del bloque `finally` es su garantía de ejecución casi absoluta. El conjunto de instrucciones contenido en su interior se procesará invariablemente en todos los escenarios lógicos: tanto si el bloque `try` concluye su "camino feliz" sin contratiempos, como si se lanza una excepción que es interceptada por un `catch`, e incluso si se produce un error no controlado que aborta la función actual. Actúa como una cláusula de finalización forzosa diseñada para mantener la integridad del sistema operativo y de la aplicación, evitando fugas de memoria o bloqueos de archivos.

Esta garantía de ejecución se mantiene en pie incluso frente a interrupciones explícitas del flujo de control, como la presencia de una sentencia `return` en medio del bloque `try` o del bloque `catch`. Cuando el entorno de ejecución encuentra un `return`, primero evalúa el valor a devolver y lo reserva temporalmente; a continuación, suspende la salida de la función para ejecutar íntegramente el bloque `finally`. Solo cuando las instrucciones del `finally` han concluido, se hace efectivo el retorno definitivo al código llamador, demostrando la prioridad ineludible de este bloque de limpieza.

```java
public class DemostracionFinally {
    public static String evaluarConReturn() {
        try {
            System.out.println("1. Entrando al bloque try.");
            // Se intenta retornar un valor, pero se suspende hasta ejecutar el finally
            return "Resultado exitoso";

        } finally {
            // Esta instrucción siempre se imprime en consola antes de salir de la función
            System.out.println("2. Ejecutando finally ANTES de que la funcion retorne el valor.");
        }
    }

    public static void main(String[] args) {
        String texto = evaluarConReturn();
        System.out.println("3. Valor recibido en el llamador: " + texto);
    }
}

```

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Excepciones Controladas y No Controladas en Java

En Java, las excepciones se dividen en dos grandes categorías según el grado de exigencia que impone el compilador para su gestión. Las excepciones **"controladas"** (_checked exceptions_) son aquellas que el compilador exige prever obligatoriamente; si un fragmento de código puede generarlas, es mandatorio rodearlo con un bloque `try-catch` o delegar la responsabilidad declarando la excepción en la firma de la función. Por el contrario, las excepciones **"no controladas"** (_unchecked exceptions_) no imponen esta restricción en tiempo de compilación, permitiendo que el código se escriba sin estructuras de manejo de errores explícitas si así se decide.

En esta clasificación, la clase `RuntimeException` juega un papel arquitectónico fundamental. En la jerarquía de objetos de Java, cualquier excepción que herede directa o indirectamente de `RuntimeException` es tratada automáticamente por el compilador como una excepción no controlada. Se parte de la premisa de que estos fallos representan errores de lógica o defectos de programación (como un fallo de segmentación en C) que deberían solucionarse corrigiendo el código fuente, en lugar de intentar recuperarse de ellos en tiempo de ejecución. Cualquier otra excepción que herede de la clase base `Exception`, pero no de `RuntimeException`, se considera controlada.

Como ejemplos típicos que los desarrolladores emplean frecuentemente, entre las excepciones controladas destacan `IOException` (para errores generales de entrada/salida) o `FileNotFoundException` (al no hallar un documento). En el bando de las no controladas, son comunes `IllegalArgumentException` (la utilizada en la clase `Calculadora`), `NullPointerException` (equivalente a desreferenciar un puntero nulo en C) o `IndexOutOfBoundsException` (al acceder fuera de los límites de un arreglo).

La decisión de diseñar o lanzar un tipo u otro depende estrictamente de la naturaleza del fallo y de si el programa tiene posibilidades reales de recuperarse:

**Situaciones donde se prefiere una excepción controlada (recuperables o dependientes del entorno):**

- Intentar abrir, leer o escribir en un archivo del disco duro que podría haber sido borrado o bloqueado por otro programa.
- Establecer comunicación con una base de datos externa o un servidor de red que podría experimentar caídas o latencias imprevistas.
- Procesar e interpretar información introducida por un usuario (como fechas o configuraciones) que no respeta el formato estricto esperado.

**Situaciones donde se prefiere una excepción no controlada (errores de lógica o defectos del código):**

- Validar los argumentos de una función matemática donde recibir un parámetro fuera de dominio (como un divisor igual a cero) indica un fallo en la lógica del código llamador.
- Intentar acceder a la posición número cien de una lista en memoria que solamente contiene diez elementos.
- Tratar de invocar el método de un objeto utilizando una variable de referencia que aún no ha sido inicializada (apunta a nulo).
- Solicitar una operación sobre un objeto cuyo estado interno actual hace que dicha acción sea completamente inválida.

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

La palabra reservada `throws` en Java se utiliza en la declaración o firma de un método para indicar que la ejecución de dicho método puede originar una o varias excepciones. Actúa como un contrato formal y explícito entre la función y cualquier código que pretenda invocarla, advirtiendo sobre las posibles condiciones anómalas que podrían escapar de su interior y que el llamador deberá estar preparado para gestionar.

Cuando en el interior de un método se invoca una rutina propensa a generar una "excepción controlada" (como acceder a un archivo físico), el compilador de Java impone una regla estricta: o bien se maneja el error internamente, o bien se delega. Si la función actual carece de la información o la responsabilidad necesaria para solucionar el problema mediante una estructura `try-catch`, se emplea `throws` al final de su cabecera. De este modo, la función notifica al compilador que rehúsa capturar el error y, en su lugar, autoriza que la excepción se propague naturalmente hacia arriba en la pila de llamadas.

Por consiguiente, declarar una excepción con `throws` representa una alternativa arquitectónica a la captura inmediata del error, permitiendo separar la detección del fallo de su resolución final. Esto resulta fundamental cuando se desarrollan funciones de bajo nivel o de utilidad general; en lugar de forzar a que estas rutinas decidan arbitrariamente cómo reaccionar ante un contratiempo, simplemente lo escalan. La responsabilidad recae entonces en los métodos de las capas superiores de la aplicación, los cuales disponen del contexto adecuado para interceptar el objeto excepción y tomar una decisión coherente con el estado global del programa.

```java
import java.io.IOException;

public class LectorArchivos {
    // El método declara que puede lanzar una excepción controlada, delegando su manejo
    public void leerConfiguracion(String ruta) throws IOException {
        // Si ocurre un error de lectura aquí, la excepción IOException escapará del método
        // sin necesidad de incluir bloques try-catch en esta zona.
        throw new IOException("El archivo no existe o esta bloqueado en la ruta: " + ruta);
    }
}

public class Sistema {
    public static void main(String[] args) {
        LectorArchivos lector = new LectorArchivos();

        // Dado que leerConfiguracion usa 'throws IOException' (excepción controlada),
        // el compilador OBLIGA a este código llamador a usar try-catch o a volver a delegar.
        try {
            lector.leerConfiguracion("C:/config.txt");

        } catch (IOException e) {
            System.out.println("Error critico durante el arranque: " + e.getMessage());
        }
    }
}

```

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

Cuando se requiere interactuar con el sistema de archivos y delegar el manejo de posibles errores de existencia, se emplea la cláusula `throws` en la cabecera del método. Al especificar una excepción controlada como `FileNotFoundException`, el compilador autoriza la omisión del bloque `catch` correspondiente. De esta manera, si el archivo solicitado no se encuentra, el objeto excepción generado interrumpe el flujo local y se propaga automáticamente hacia la función llamadora, transfiriéndole la responsabilidad de informar al usuario o de ejecutar un plan de contingencia.

A pesar de delegar la resolución del error principal, la función actual sigue manteniendo la responsabilidad de gestionar correctamente los recursos del sistema operativo que intenta manipular. Para resolver esta necesidad técnica sin capturar la excepción, se utiliza una estructura `try-finally`. Este diseño asegura que, si el archivo logra abrirse pero ocurre un fallo inesperado durante su lectura, el entorno de ejecución forzará el paso por el bloque `finally` para cerrar el fichero de forma segura antes de que el error abandone definitivamente el método.

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class LectorDatos {
    // La firma advierte que el método propagará excepciones controladas
    public void extraerInformacion(String ruta) throws FileNotFoundException, IOException {
        FileInputStream flujoEntrada = null;

        try {
            // Si el fichero no existe, se lanza FileNotFoundException.
            // Al no haber catch, el control salta inmediatamente al bloque finally.
            flujoEntrada = new FileInputStream(ruta);

            System.out.println("Fichero abierto correctamente. Iniciando lectura...");
            // Aquí irían las operaciones de lectura que podrían lanzar otras excepciones

        } finally {
            // Este bloque garantiza el cierre del recurso antes de la propagación.
            // Es vital comprobar que no sea nulo, por si el error ocurrió al intentar abrirlo.
            if (flujoEntrada != null) {
                flujoEntrada.close();
                System.out.println("Cierre del fichero garantizado en finally.");
            }
        }
    }
}
```

En el código expuesto, la firma del método actúa como un contrato transparente. Si la instanciación de `FileInputStream` fracasa, la variable `flujoEntrada` conserva su valor nulo original; el flujo salta al bloque `finally`, omite la instrucción de cierre y la excepción original se propaga intacta hacia arriba en la pila de llamadas. Por el contrario, si la apertura es exitosa pero emerge una anomalía posterior, el bloque `finally` invoca el método `close()`, liberando el descriptor de archivo y protegiendo la memoria del sistema operativo, todo ello sin interferir en la propagación natural del error hacia el código llamador.

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

En el lenguaje Java, es sintácticamente correcto incluir excepciones no controladas, como `RuntimeException` o cualquiera de sus clases derivadas (por ejemplo, `IllegalArgumentException`), dentro de la cláusula `throws` en la firma de un método. Sin embargo, realizar esta declaración explícita no altera la naturaleza intrínseca de la excepción. Dado que sigue perteneciendo a la categoría de las no controladas, el compilador **no obligará** al método llamador a envolver la invocación en una estructura `try-catch`, ni le exigirá volver a declararla para propagarla. La gestión del error seguirá siendo completamente opcional a nivel de compilación.

El propósito principal de declarar una excepción no controlada mediante `throws` es puramente **documental e informativo**. Al incluirla en la firma, se establece un contrato claro en la Interfaz de Programación de Aplicaciones (API) de la clase, comunicando a otros desarrolladores bajo qué circunstancias lógicas la operación podría fallar de manera abrupta. Esta práctica es especialmente útil cuando se genera documentación técnica automatizada (como _Javadoc_), ya que permite detallar las precondiciones que deben cumplir los argumentos antes de invocar la rutina, mejorando la comprensión del encapsulamiento y del comportamiento del objeto.

Por ejemplo, al diseñar el método que calcula la raíz cuadrada, añadir `throws IllegalArgumentException` a su firma sirve como una advertencia visual directa. Aunque el sistema no fuerce la captura del error, esta señal preventiva indica al programador que consume la clase que debe asegurarse de no enviar números negativos. Con esta información, el código llamador puede decidir de forma consciente cómo protegerse: ya sea validando los datos de entrada previamente mediante sentencias condicionales clásicas, o decidiendo implementar un bloque `try-catch` si los números provienen de una fuente externa e impredecible.

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

La recomendación general en el diseño de software establece que las excepciones **controladas** (como `IOException`) deben emplearse para situaciones excepcionales pero previsibles y, sobre todo, recuperables. Suelen estar causadas por factores externos al programa y dependientes del entorno, como redes caídas o archivos inexistentes. Al forzar su declaración o captura, se garantiza la resiliencia del sistema frente a contingencias ambientales. Por el contrario, las excepciones **no controladas** (como `IllegalArgumentException`) se reservan para errores de programación o fallos en la lógica interna, como proporcionar parámetros inválidos o acceder a referencias nulas. Estos representan defectos lógicos (_bugs_) que el desarrollador debería evitar o corregir en el código fuente mediante validaciones previas, en lugar de intentar recuperarse de ellos en tiempo de ejecución.

La existencia de estas dos categorías no es una norma universal en la programación. De hecho, Java es uno de los pocos lenguajes de uso masivo que implementa y hace cumplir estrictamente el concepto de excepciones controladas a nivel de compilador. Lenguajes con orientación a objetos que sucedieron o compiten con Java, como C#, Python, Ruby o incluso Kotlin, optaron deliberadamente por no incluir excepciones controladas en su diseño. Asimismo, en C++, aunque se introdujo el mecanismo de excepciones, todas operan de forma equivalente a las no controladas, delegando la responsabilidad de su captura a la disciplina del desarrollador.

En la inmensa mayoría de los lenguajes que disponen de una única opción, el modelo predominante es el de las **excepciones no controladas**. La evolución de la ingeniería de software y la experiencia comunitaria han revelado que, si bien la obligatoriedad impuesta por las excepciones controladas resulta teóricamente muy sólida para garantizar la robustez, en la práctica suele generar código excesivamente verboso y un acoplamiento muy rígido entre las capas de la aplicación. Por ello, el paradigma moderno prefiere utilizar exclusivamente excepciones no controladas, confiando la correcta gestión de los errores a una documentación exhaustiva (como el uso informativo de `throws`), a una arquitectura limpia y a la implementación de pruebas automatizadas.

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Es una práctica completamente válida y muy habitual lanzar una nueva excepción desde el interior de un bloque `catch`. Esta técnica, conocida como "traducción" o "envoltura" de excepciones (_exception wrapping_), se utiliza para mejorar la encapsulación del sistema. Cuando una rutina de bajo nivel captura un error técnico muy específico (como un fallo de acceso al disco rígido), en lugar de propagar esos detalles de infraestructura al código llamador, se instancia y lanza una nueva excepción de más alto nivel. De este modo, se representa el problema en términos del dominio de la aplicación, ocultando la complejidad subyacente.

```java
public void cargarConfiguracion(String ruta) {
    try {
        // Código hipotético que podría fallar al acceder al sistema de archivos
        abrirArchivoFisico(ruta);

    } catch (IOException e) {
        // Se lanza una NUEVA excepción adaptada al contexto, a menudo adjuntando la causa original
        throw new RuntimeException("Fallo al inicializar el modulo de configuracion del sistema.", e);
    }
}
```

Por otro lado, también es posible e igualmente útil relanzar exactamente la **misma excepción** que acaba de ser interceptada. Para lograrlo, simplemente se emplea la instrucción `throw` seguida de la variable de referencia definida en los parámetros del bloque `catch`. El sentido de realizar esta maniobra radica en la necesidad de ejecutar una acción parcial en el nivel actual de la pila de llamadas, pero sin asumir la responsabilidad de dar por solucionado el fallo de manera definitiva.

```java
public void procesarTransaccion(double monto) {
    try {
        Calculadora calc = new Calculadora();
        calc.calcularRaiz(monto); // Podría lanzar IllegalArgumentException

    } catch (IllegalArgumentException e) {
        // 1. Se realiza una acción local: registrar el error silenciosamente en un archivo de auditoría
        System.out.println("LOG DE AUDITORIA: Intento de transaccion matematica invalida.");

        // 2. Se relanza la MISMA excepción para que la capa superior decida cómo avisar al usuario
        throw e;
    }
}
```

En la arquitectura de software, ambas técnicas son fundamentales para un manejo de errores robusto. Mientras que la traducción de excepciones evita que los detalles de implementación se filtren hacia capas superiores, relanzar la misma excepción actúa como un punto de control intermedio. Esto permite auditar el error, deshacer operaciones parciales o liberar recursos específicos que no encajan en un bloque `finally`, para acto seguido permitir que el flujo de la excepción continúe su propagación natural.

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

En la programación orientada a objetos, el mecanismo por el cual una excepción se convierte en la "causa" de otra se denomina encadenamiento de excepciones (_exception chaining_). Esto sucede cuando un bloque `catch` intercepta un error de bajo nivel (por ejemplo, un fallo técnico al leer un archivo) y, en lugar de propagarlo directamente, instancia y lanza una nueva excepción de más alto nivel que explica el problema en términos lógicos del dominio de la aplicación. Para no perder la valiosa información técnica del fallo original, el objeto de la primera excepción se pasa como argumento al constructor de la nueva, quedando encapsulado de forma segura en su interior como la "causa raíz" (_root cause_).

```java
// Definición de una excepción personalizada de alto nivel
public class ConfiguracionException extends RuntimeException {
    // El constructor recibe el mensaje y el objeto excepción que actuará como causa
    public ConfiguracionException(String mensaje, Throwable causa) {
        super(mensaje, causa); // Se inicializa la causa en la clase base
    }
}

public class GestorSistema {
    public void cargarAjustes() {
        try {
            // Se simula un error técnico de bajo nivel (ej. fallo del sistema de archivos)
            throw new java.io.IOException("Acceso denegado al disco C:");

        } catch (java.io.IOException e) {
            // Se captura el error técnico y se encapsula como CAUSA de un error lógico
            throw new ConfiguracionException("No se pudo inicializar el modulo del sistema.", e);
        }
    }

    public static void main(String[] args) {
        GestorSistema gestor = new GestorSistema();
        // Si no se captura aquí, el programa fallará mostrando ambas excepciones
        gestor.cargarAjustes();
    }
}
```

Cuando una excepción encadenada de este tipo interrumpe el programa por no ser controlada en ningún punto, la información sobre la causa sí resulta visible. El entorno de ejecución de Java imprime automáticamente la traza de la pila (_stack trace_) en la consola, mostrando primero la excepción de alto nivel que desencadenó la salida. A continuación, añade la cláusula `Caused by:` (Causado por:) seguida del tipo, el mensaje y la traza de la excepción original que fue encapsulada. Esta salida proporciona una visión integral para la depuración: permite comprender qué operación lógica falló a nivel general y, simultáneamente, cuál fue el error técnico exacto y la línea de código específica que originó el colapso desde las capas inferiores del sistema.
