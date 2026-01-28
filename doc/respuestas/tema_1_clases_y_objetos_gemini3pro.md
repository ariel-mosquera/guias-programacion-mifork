# Cuestionario: Clases y Objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

La Programación Orientada a Objetos (POO) se fundamenta en cuatro pilares esenciales que transforman la forma de organizar el código respecto a la programación estructurada en C:

- **Abstracción**: Consiste en extraer las características esenciales de un objeto (sus datos y comportamientos) ignorando los detalles complejos de su implementación interna.

- **Encapsulamiento**: Mecanismo que agrupa datos y métodos en una única entidad (la clase) y restringe el acceso directo a sus componentes internos. Protege la integridad de la información de manera similar a cómo se ocultarían variables estáticas dentro de un módulo en C, permitiendo el acceso solo a través de funciones públicas.

- **Herencia**: Permite definir nuevas clases basadas en otras ya existentes, adquiriendo sus propiedades y comportamientos. Fomenta la reutilización de código sin necesidad de "copiar y pegar" funciones.

- **Polimorfismo**: Otorga la capacidad a objetos de diferentes tipos de responder al mismo mensaje o método de formas distintas. En el contexto de C, esto sería comparable a invocar una función a través de un puntero genérico, donde la ejecución real depende dinámicamente de la estructura subyacente.

## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

Entre los lenguajes más destacados que implementan este paradigma se encuentran **Java**, **C++**, **C#** y **Python**.

- **C++**: Es la evolución directa del lenguaje C, diseñada para añadir características de orientación a objetos sin perder la eficiencia ni el control del hardware. A diferencia de C, permite la creación de clases y la gestión de herencia, aunque mantiene la posibilidad de escribir programación estructurada clásica y el uso explícito de punteros para la gestión de memoria.

- **Java y C#**: Son lenguajes que fuerzan una adopción más estricta del paradigma. En Java, no existen las funciones globales o variables fuera de una clase, algo común en C; todo el código debe pertenecer a una estructura de objeto. Además, ambos lenguajes abstraen la gestión de memoria mediante un recolector de basura, eliminando la necesidad de realizar `malloc` o `free` manualmente.

- **Python**: Es un lenguaje interpretado y multiparadigma donde absolutamente todo, incluso los tipos de datos básicos como enteros, son objetos. Ofrece una gran flexibilidad dinámica en tiempo de ejecución.

## 3. Los paradigmas anteriores a la POO. ¿Qué es la programación estructurada? y, ¿Qué es la programación modular?

### Programación Estructurada

La programación estructurada es un paradigma orientado a mejorar la claridad, calidad y tiempo de desarrollo de software mediante el uso de subrutinas y tres estructuras de control básicas:

- **Secuencia**: Ejecución ordenada de instrucciones
- **Selección**: Condiciones como `if` o `switch`
- **Iteración**: Bucles como `for` o `while`

Surgió como una solución al código desordenado provocado por el abuso de la instrucción `GOTO`, promoviendo un flujo de ejecución lógico que se lee de arriba hacia abajo. En C, este es el estilo estándar de escritura dentro de una función.

### Programación Modular

La programación modular representa un nivel superior de organización, donde la funcionalidad del programa se subdivide en piezas independientes y manejables llamadas **módulos**. Cada módulo agrupa procedimientos y datos relacionados, definiendo una interfaz clara (qué hace) separada de su implementación (cómo lo hace).

En C, esto se materializa mediante la separación de código en:

- Archivos de cabecera (`.h`) para las declaraciones públicas
- Archivos de fuente (`.c`) para las definiciones internas

Aunque la programación modular introduce una forma primitiva de encapsulamiento, se diferencia de la Orientación a Objetos en que los datos y las funciones que los manipulan siguen siendo entidades conceptualmente distintas.

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Un objeto se define fundamentalmente por tres elementos:

- **Estado**: Representa la situación en la que se encuentra el objeto, definido por el valor de sus atributos o variables internas. Para un programador de C, esto es comparable a los campos de una `struct`.

- **Comportamiento**: Determina las acciones que el objeto puede realizar. Se implementa a través de métodos, que son funcionalmente análogos a las funciones en C. La diferencia radica en que el comportamiento "vive" dentro de la definición del objeto.

- **Identidad**: La propiedad que distingue a un objeto de todos los demás, incluso si sus estados son idénticos. En C, esto se entiende mediante las direcciones de memoria: dos variables `struct` pueden tener los mismos datos, pero si están en direcciones diferentes, son entidades distintas.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

### Definiciones

- **Clase**: La plantilla o molde conceptual que define la estructura y el comportamiento de un conjunto de objetos similares. Para un programador de C, es comparable a un `typedef struct`, pero enriquecido con métodos. Una clase es abstracta y no contiene datos propios ni ocupa memoria.

- **Objeto**: La materialización concreta de esa clase en la memoria del ordenador. La relación es análoga a la que existe entre un plano arquitectónico y la casa construida.

- **Instancia**: El término técnico preciso para referirse a un objeto específico creado a partir de una clase determinada. Decir que "el objeto X es una instancia de la clase Y" significa que X fue creado siguiendo el molde definido por Y.

### Lenguajes basados en prototipos

No todos los lenguajes orientados a objetos utilizan clases. Existen lenguajes "basados en prototipos" (como JavaScript en su concepción original o Self) donde no existen las clases como moldes maestros. La creación de nuevos objetos se realiza clonando objetos existentes (prototipos) y modificándolos.

## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la recolección de basura?

### Almacenamiento en memoria

En la mayoría de los lenguajes puramente orientados a objetos como Java, los objetos se almacenan en el **Heap** (montículo). Esto es análogo a utilizar `malloc` en C para reservar memoria dinámica. Aunque la variable que referencia al objeto se ubica en el **Stack** (pila), esta variable solo contiene una dirección de memoria que apunta a donde realmente residen los datos del objeto en el Heap.

No obstante, esto no es universal:

- **C++**: Ofrece flexibilidad de elegir entre Stack (gestión automática) o Heap (usando `new`)
- **Java**: Obliga a que todos los objetos sean referencias al Heap

### Recolección de basura

La **recolección de basura** (Garbage Collection) es el mecanismo automático que gestiona la liberación de memoria en el Heap. En C, cada vez que se reserva memoria con `malloc`, es responsabilidad del programador liberarla con `free`. Los lenguajes modernos con recolección de basura ejecutan un proceso en segundo plano que detecta cuándo un objeto ya no es accesible y libera esa memoria automáticamente.

## 7. ¿Qué es un método? ¿Qué es la sobrecarga de métodos?

### Método

Un **método** es una subrutina o función definida dentro de una clase que describe el comportamiento de los objetos instanciados a partir de ella. Aunque en C estamos acostumbrados a declarar funciones que reciben una estructura como argumento explícito, en la programación orientada a objetos el método reside conceptualmente "dentro" del objeto. Esto permite invocar la acción directamente sobre la instancia sin necesidad de pasarla manualmente.

### Sobrecarga de métodos

La **sobrecarga de métodos** permite definir múltiples métodos con el mismo nombre dentro de la misma clase, siempre y cuando su lista de parámetros (su "firma") sea diferente.

En C estándar esto no es posible: si intentáramos crear `imprimir(int x)` e `imprimir(float x)`, el compilador arrojaría un error. Obligándonos a usar nombres distintos como `imprimir_int` e `imprimir_float`.

En los lenguajes orientados a objetos, el compilador distingue qué método invocar basándose en los tipos y la cantidad de argumentos proporcionados.

## 8. Ejemplo mínimo de clase en Java: Punto

Clase con dos atributos `x` e `y` y un método `calculaDistanciaAOrigen`:

```java
// Definición de la clase (el molde)
class Punto {
    // Atributos con visibilidad por defecto (package-private)
    // Similar a los campos de un struct en C
    double x;
    double y;

    // Método de instancia
    // Equivale a una función que recibe implícitamente la estructura
    double calculaDistanciaAOrigen() {
        // Acceso directo a x e y sin necesidad de operador flecha (->)
        // Math.sqrt es parte de la librería estándar de Java
        return Math.sqrt(x * x + y * y);
    }
}

// Clase principal para ejecutar el ejemplo
class Main {
    public static void main(String[] args) {
        // 1. Instanciación
        // 'miPunto' es una referencia (similar a un puntero seguro) al objeto en el Heap
        Punto miPunto = new Punto();

        // 2. Acceso y modificación de atributos
        miPunto.x = 3.0;
        miPunto.y = 4.0;

        // 3. Uso del método
        double distancia = miPunto.calculaDistanciaAOrigen();

        System.out.println("La distancia es: " + distancia);
    }
}
```

En este ejemplo, la definición de `class Punto` fusiona los datos y la lógica en una sola estructura. El método `calculaDistanciaAOrigen` está "dentro" del ámbito de `Punto`, por lo que puede acceder a `x` e `y` directamente. No es necesario pasar un puntero a la estructura como argumento, ya que el contexto del objeto (`this`) está implícito.

En el bloque `main`, la instrucción `new Punto()` realiza una tarea equivalente a un `malloc` en C. La variable `miPunto` no almacena el objeto en sí mismo, sino la dirección donde vive.

## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es static y para qué vale? ¿Para qué se combina con final?

### Punto de entrada

El punto de entrada en Java es el método `public static void main(String[] args)`. A diferencia de C, donde `main` es una función libre y global, en Java todo debe estar contenido dentro de una clase.

### El modificador static

Esta palabra clave es fundamental: indica al entorno de ejecución (JVM) que el método puede ser invocado sin necesidad de instanciar un objeto previo de esa clase.

El modificador `static` no es exclusivo del método `main`; tiene un uso más amplio para definir miembros de clase:

- Un atributo o método `static` deja de pertenecer a una instancia concreta y pasa a pertenecer a la clase en sí misma
- Es comparable a una variable global en C, pero encapsulada dentro del espacio de nombres de la clase
- Todos los objetos de esa clase compartirán la misma copia de una variable estática

### Combinación static final

La combinación `static final` se utiliza habitualmente para declarar constantes globales:

- `final` indica que el valor no puede cambiar una vez asignado (inmutabilidad)
- `static` para que exista una única copia en memoria asociada a la clase

Así, una declaración como `static final double PI = 3.1416;` es el equivalente directo y seguro en tipos de la directiva `#define PI 3.1416` de C.

## 10. Compilación y ejecución en Java. ¿Java es compilado? ¿Qué es la máquina virtual? ¿Qué es el byte-code?

### Proceso de construcción

El proceso de construcción en Java se divide en dos etapas:

1. **Compilación**: Se utiliza el comando `javac NombreArchivo.java` para compilar el código fuente. A diferencia de `gcc` en C, que traduce directamente a código máquina nativo, `javac` traduce el código fuente a un formato intermedio binario denominado **byte-code**.

2. **Ejecución**: Se utiliza el comando `java NombreClase` para ejecutar el programa. Este comando invoca a la **Máquina Virtual de Java** (JVM), un software que actúa como un ordenador simulado.

### Byte-code y ficheros .class

- El resultado de la compilación es un fichero con extensión `.class` (por ejemplo, `NombreArchivo.class`), que no es ejecutable directamente por el sistema operativo
- La JVM carga los ficheros `.class`, lee las instrucciones en byte-code y las traduce dinámicamente a las instrucciones nativas del procesador
- Java es un lenguaje compilado (hacia byte-code) e interpretado (por la JVM), aunque las JVM modernas utilizan técnicas JIT (Just-In-Time) para compilar internamente a código nativo

### Portabilidad

El byte-code es la clave de la portabilidad de Java. Mientras que en C es necesario recompilar el código fuente para cada arquitectura distinta (Windows, Linux, macOS), en Java el mismo fichero `.class` funciona en cualquier sistema que tenga instalada una JVM.

## 11. ¿Qué es new? ¿Qué es un constructor?

### El operador new

El operador `new` en Java cumple una función análoga a `malloc` en C: su propósito es solicitar memoria dinámica al sistema para alojar una nueva instancia de una clase. Sin embargo, `new` es de más alto nivel y más seguro:

- No requiere especificar el tamaño en bytes
- La máquina virtual ya conoce el tamaño necesario de la clase
- Devuelve automáticamente una referencia del tipo correcto
- Evita la necesidad de hacer casting de punteros `void*`

### Constructor

El **constructor** es un método especial que se invoca automáticamente e inmediatamente después de que `new` ha reservado la memoria. Su objetivo es inicializar el estado del objeto, asegurando que nazca con valores válidos.

Se distingue sintácticamente porque:

- Tiene exactamente el mismo nombre que la clase
- No tiene tipo de retorno, ni siquiera `void`

### Ejemplo: Clase Empleado

```java
class Empleado {
    // Atributos de la clase
    String dni;
    String nombre;
    String apellidos;

    // Constructor: mismo nombre que la clase, sin tipo de retorno
    Empleado(String dni, String nombre, String apellidos) {
        // 'this' referencia a la instancia actual que se está inicializando
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }
}

class Main {
    public static void main(String[] args) {
        // Al usar 'new', se obliga a pasar los argumentos definidos en el constructor
        Empleado e = new Empleado("12345678A", "Juan", "Pérez");
    }
}
```

## 12. ¿Qué es la referencia this? Ejemplo en la clase Punto

La referencia `this` es una variable oculta que existe dentro de cada método de instancia y que actúa como un puntero al propio objeto que está ejecutando dicho método. Para un programador de C, `this` es exactamente equivalente al puntero a la estructura que se pasa como primer argumento.

Su función principal es permitir que el código dentro de la clase acceda a sus propios miembros, y resulta especialmente crucial para resolver ambigüedades cuando un parámetro tiene el mismo nombre que un atributo.

### Variaciones en otros lenguajes

Aunque el concepto es universal, la palabra clave varía:

- **C++, Java, C#, JavaScript**: Utilizan `this`
- **Python, Rust, Swift**: Emplean convencionalmente `self`

En Python, esta referencia debe declararse explícitamente como el primer parámetro de cada método, mientras que en Java y C++ es implícita.

### Ejemplo: Punto con this

```java
class Punto {
    double x;
    double y;

    // Constructor donde los parámetros se llaman igual que los atributos
    Punto(double x, double y) {
        // 'this.x' se refiere al atributo del objeto
        // 'x' (sin this) se refiere al parámetro local
        this.x = x;
        this.y = y;
    }

    void actualizarPosicion(double x, double y) {
        // Aquí también es necesario 'this' para desambiguar
        this.x = x;
        this.y = y;
    }
}
```

## 13. Método distanciaA en la clase Punto

Este método ilustra cómo los objetos pueden interactuar entre sí. El método tiene acceso a dos contextos diferentes simultáneamente:

- El estado del objeto que ejecuta la acción (referenciado por `this`)
- El estado del objeto que se pasa como argumento

```java
class Punto {
    double x, y;

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Método que recibe otro objeto de la misma clase
    double distanciaA(Punto otroPunto) {
        // Accedemos a 'x' de la instancia actual y a 'x' del parámetro
        double dx = this.x - otroPunto.x;
        double dy = this.y - otroPunto.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

class Main {
    public static void main(String[] args) {
        Punto p1 = new Punto(0, 0);
        Punto p2 = new Punto(3, 4);

        // Se pide a p1 que calcule su distancia hasta p2
        double d = p1.distanciaA(p2);
        System.out.println("Distancia: " + d); // Salida: 5.0
    }
}
```

Dentro de la definición de la clase, se tiene permiso total para acceder a los atributos privados de cualquier instancia de la misma clase. El control de acceso se realiza a nivel de clase, no estrictamente a nivel de objeto individual.

## 14. Paso de parámetros: ¿Copia o referencia?

En Java, el mecanismo de paso de parámetros es estrictamente **por valor**, aunque esto tiene matices importantes:

### Objetos (como Punto)

Cuando se pasa un objeto a un método, lo que se copia no es el objeto entero, sino el valor de la referencia (la dirección de memoria). Para un programador de C, esto es exactamente igual que pasar un puntero por valor.

Dado que tanto el método llamante como el llamado tienen copias que apuntan a la misma dirección de memoria, si dentro del método se modifican los atributos del objeto, esos cambios **sí son visibles fuera del método**, afectando al objeto original.

### Tipos primitivos (int, double, etc.)

Cuando se pasa un tipo primitivo, se copia el valor literal del dato. Cualquier modificación que se realice sobre esa variable dentro del método se hace sobre una copia local que vive exclusivamente en el Stack. Por tanto, modificar un primitivo dentro de una función **no tiene ningún efecto** sobre la variable original.

## 15. El método toString() en Java

El método `toString()` en Java es un mecanismo estandarizado para obtener una representación textual de un objeto, útil principalmente para propósitos de depuración y registro (logging).

### Equivalentes en otros lenguajes

Aunque el nombre específico varía, la funcionalidad es idéntica:

- **Python**: `__str__`
- **C#**: `ToString()` (con mayúscula)
- **JavaScript**: `toString()`

### Ejemplo: toString() en la clase Punto

```java
class Punto {
    double x, y;

    Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Sobrescritura para personalizar la representación en texto
    @Override
    public String toString() {
        // Devuelve una cadena formateada con los valores actuales
        return "(" + x + ", " + y + ")";
    }
}

class Main {
    public static void main(String[] args) {
        Punto p = new Punto(5, 10);

        // Al usar println con un objeto, Java llama internamente a p.toString()
        System.out.println(p); // Salida: (5.0, 10.0)

        // También se invoca automáticamente en concatenaciones
        String mensaje = "La coordenada es " + p;
    }
}
```

La anotación `@Override` es opcional pero muy recomendable, ya que informa al compilador de nuestra intención de sustituir un método heredado.

## 16. ¿Una clase es como un struct en C?

Efectivamente, una `struct` en C es el precursor conceptual más cercano a una clase, ya que ambas cumplen la función fundamental de definir un nuevo tipo de dato compuesto que agrupa variables relacionadas.

### Similitudes

Desde el punto de vista de la memoria, una instancia de una clase simple en Java y una variable `struct` en C son sorprendentemente similares: ambas son bloques contiguos de memoria que almacenan los valores de sus atributos.

### Diferencias fundamentales

**Lo que le falta al struct para ser como una clase**:

1. **Integración del comportamiento**: En C, los datos (la estructura) y la lógica (las funciones) viven separados. Para simular una clase, sería necesario incluir punteros a funciones dentro de la `struct`, algo complejo y manual.

2. **Control de acceso**: Las `struct` carecen de mecanismos de control de acceso. Por defecto, todos los miembros son públicos. Una clase añade modificadores como `private` o `protected`.

3. **Herencia y Polimorfismo**: Las estructuras no soportan nativamente estos pilares.

## 17. Emulación de la clase Punto en C

Para desmitificar el funcionamiento interno de las clases, es muy ilustrativo implementar el mismo comportamiento utilizando C estándar.

La "clase" se convierte simplemente en un `struct` que define el diseño de memoria. Sin embargo, dado que en C no existen funciones dentro de estructuras, la lógica reside en una función global externa.

El truco fundamental es que la función debe aceptar explícitamente un puntero a la estructura sobre la que va a operar. Esto revela la verdadera naturaleza de los métodos: no son más que funciones estándar que reciben un contexto de datos sobre el que trabajar.

En este esquema, la referencia `this` aparece "al desnudo". Lo que en Java es una palabra clave implícita, en la emulación en C se convierte en el primer argumento de la función.

### Código C

```c
#include <math.h>
#include <stdio.h>

// 1. Definición de la estructura de datos (el "Estado")
typedef struct {
    double x;
    double y;
} Punto;

// 2. Definición del comportamiento (el "Método")
// Recibe explícitamente el puntero 'this' para saber sobre qué datos operar
double punto_calcularDistanciaAOrigen(Punto* this) {
    // Accedemos a los miembros usando el operador flecha sobre el puntero this
    return sqrt(this->x * this->x + this->y * this->y);
}

int main() {
    // Instanciación en el Stack
    Punto p = {3.0, 4.0};

    // Llamada explícita pasando la dirección de memoria (&p)
    // En Java esto sería: p.calcularDistanciaAOrigen();
    double d = punto_calcularDistanciaAOrigen(&p);

    printf("Distancia: %f\n", d);
    return 0;
}
```

Esto demuestra que `this` no es más que un puntero convencional que el compilador de Java pasa automáticamente en cada llamada a un método de instancia.
