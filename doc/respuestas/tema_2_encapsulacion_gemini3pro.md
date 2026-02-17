<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información

La encapsulación y la ocultación de información son conceptos inseparables que buscan la creación de componentes de software robustos, modulares y fáciles de mantener. La encapsulación se encarga de agrupar en una única unidad (la clase) tanto los datos (atributos) como las operaciones (métodos) que los manipulan, rompiendo con la separación tradicional de C donde las estructuras de datos (struct) y las funciones suelen estar desconectadas. Por su parte, la ocultación de información persigue restringir el acceso a los detalles internos de esa unidad, exponiendo solo lo estrictamente necesario para su uso.

El objetivo fundamental de estos mecanismos es reducir el acoplamiento entre las distintas partes de un programa. Al ocultar la representación interna de los datos (por ejemplo, haciendo private los atributos), se evita que el código externo dependa de detalles de implementación que podrían cambiar en el futuro. Esto establece una distinción clara entre la interfaz (qué hace el objeto y cómo interactuar con él) y la implementación (cómo lo hace internamente), garantizando que los cambios dentro de una clase no provoquen una reacción en cadena de errores en el resto del sistema.

Ventajas principales de la ocultación de información:

* Integridad de los datos: Al impedir el acceso directo a los atributos, se asegura que el estado del objeto solo pueda modificarse a través de métodos controlados, evitando valores inválidos o inconsistentes.

* Mantenibilidad y Flexibilidad: Permite cambiar la estructura interna de los datos o optimizar el código de los métodos sin que el código que usa la clase (el cliente) se vea afectado o tenga que ser reescrito.

* Abstracción: Reduce la carga cognitiva del programador, ya que para usar un objeto solo necesita entender su interfaz pública (se explica en el siguiente ejercicio), sin necesidad de comprender la complejidad de su funcionamiento interno (caja negra).

* Depuración simplificada: Si un dato tiene un valor incorrecto, el error se localiza fácilmente en los métodos de la propia clase, en lugar de tener que buscar en todo el programa dónde se modificó esa variable global o estructura.

## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información

La interfaz pública de una clase o un objeto constituye el conjunto de métodos y atributos (generalmente constantes) declarados con el modificador de acceso public, quedando así expuestos para ser utilizados por cualquier otra parte del programa. En analogía con el lenguaje C, se puede comparar con los prototipos de funciones declarados en un archivo de cabecera (.h), que definen cómo se debe invocar una funcionalidad sin revelar el código fuente de su implementación (.c). Es el contrato que la clase ofrece al exterior, especificando qué acciones puede realizar y qué parámetros requiere, pero sin detallar los algoritmos internos.

Esta interfaz actúa como una capa de abstracción o frontera que separa el uso del objeto de su funcionamiento interno. Al interactuar con una instancia, el código cliente (quien usa la clase) solo necesita conocer la firma de los métodos públicos (nombre, parámetros y tipo de retorno). Todo lo que no pertenezca a esta interfaz pública se considera detalle de implementación y debe permanecer inaccesible, tratando al objeto como una "caja negra" donde solo importan las entradas y las salidas definidas.

La relación con la ocultación de información es directa y complementaria: la interfaz pública es la única vía de acceso permitida a los datos ocultos. Mientras que la ocultación protege el estado interno (atributos private) para evitar manipulaciones indebidas, la interfaz pública proporciona los mecanismos controlados (métodos getters, setters u operaciones de negocio) para gestionar dicho estado de forma segura. Esta separación garantiza que se pueda modificar la implementación interna —por ejemplo, cambiando una estructura de datos u optimizando un algoritmo— sin afectar al código externo, siempre y cuando se mantenga inalterada la interfaz pública.

## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

Se debe diseñar la interfaz pública con extremo cuidado porque esta establece un "contrato" vinculante entre la clase y el resto del sistema. Al igual que ocurre con las cabeceras de una librería en C, una vez que se definen y publican los métodos públicos, otros módulos del programa empezarán a depender de ellos para funcionar. Si la interfaz está mal diseñada —por ejemplo, exponiendo detalles internos innecesarios o nombrando métodos de forma ambigua—, se fomenta un acoplamiento excesivo y un uso incorrecto de la clase, lo que degrada la calidad y la robustez del software a largo plazo.

Cambiar la interfaz pública una vez que ya está siendo utilizada es una tarea compleja y costosa, a menudo descrita como "romper el código". Si se modifica la firma de un método público (su nombre, parámetros o tipo de retorno) o se elimina, cualquier parte del programa que invoque a ese método dejará de compilar inmediatamente, obligando a realizar una refactorización en cascada por todo el proyecto para adaptar las llamadas al nuevo diseño. Por el contrario, la gran ventaja de la encapsulación es que permite cambiar libremente la implementación privada sin estos efectos secundarios; por ello, una buena práctica es mantener la interfaz pública lo más reducida y estable posible.

## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las invariantes de clase se definen como el conjunto de condiciones lógicas o restricciones que deben mantenerse verdaderas durante toda la vida útil de un objeto para que este se considere en un estado válido y consistente. A diferencia de una simple variable en C, que puede contener cualquier valor permitido por su tipo de dato (por ejemplo, un int que acepta cualquier número), un objeto en POO suele representar una entidad con reglas de negocio específicas. Un ejemplo clásico sería una clase Triangulo donde la suma de dos lados siempre debe ser mayor al tercero, o una clase Fecha donde el atributo dia nunca puede ser mayor que 31. Estas reglas son las invariantes.

La ocultación de información es el mecanismo técnico que permite garantizar el cumplimiento de estas invariantes. Si los atributos de la clase fueran públicos (accesibles directamente como los campos de un struct en C), cualquier parte del programa podría modificar los datos arbitrariamente, asignando valores que violen las reglas (por ejemplo, triangulo.ladoA = -50), rompiendo así la consistencia del objeto sin que la clase pueda evitarlo. Al no haber barreras, la responsabilidad de verificar la validez de los datos recaería dispersamente en cada lugar donde se usa el objeto, lo cual es propenso a errores.

Al ocultar los datos declarándolos private, se obliga a que cualquier modificación del estado pase necesariamente por los métodos de la clase (constructores y setters). Esto permite centralizar la lógica de validación en estos puntos de entrada. Si un valor propuesto viola una invariante, el método puede rechazar la asignación o lanzar una excepción, asegurando que el estado interno del objeto nunca se corrompa. De esta forma, la clase actúa como guardiana de su propia integridad, garantizando que, en cualquier momento que se inspeccione el objeto, este cumplirá con las reglas definidas en su diseño.

## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

Para implementar la clase Punto aplicando la ocultación de información, se definen los atributos x e y bajo el modificador de acceso private. Esto establece una diferencia fundamental respecto a una struct en C, ya que impide el acceso directo a las coordenadas desde fuera de la clase (por ejemplo, punto.x = 5.0 daría un error de compilación). El estado interno del objeto queda así protegido y solo es manipulable a través de los métodos que la clase decida exponer. En este caso, el cálculo de la distancia se encapsula dentro del método calcularDistanciaAOrigen, el cual tiene acceso directo a los atributos privados por estar dentro del mismo ámbito de clase.

La interfaz pública de esta clase está constituida por aquellos miembros declarados como public: el constructor Punto(double x, double y) y el método calcularDistanciaAOrigen(). Estos elementos forman el contrato que la clase ofrece al exterior. El modificador public permite que cualquier otra clase del programa invoque estos métodos, mientras que private restringe la visibilidad estrictamente al bloque de código de la clase Punto. Si en el futuro se decidiera cambiar la implementación interna de coordenadas cartesianas a polares, solo habría que modificar el código privado, manteniendo intacta la interfaz pública y sin romper el código de quien use la clase.

```java
public class Punto {
    // Atributos privados: Ocultación de información (Estado interno)
    private double x;
    private double y;

    // Constructor público: Parte de la interfaz pública
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Método público: Parte de la interfaz pública (Comportamiento)
    public double calcularDistanciaAOrigen() {
        // Puede acceder a x e y porque está dentro de la misma clase
        return Math.sqrt(Math.pow(this.x, 2) + Math.pow(this.y, 2));
    }
}
```

## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En el lenguaje Java, los modificadores de acceso public y private se aplican principalmente a los miembros de una clase, es decir, a sus atributos (variables de estado) y a sus métodos (comportamiento), incluyendo los constructores. Cuando se aplican a estos elementos, determinan la visibilidad y accesibilidad desde otras partes del código: public permite el acceso desde cualquier clase del universo de la aplicación, mientras que private restringe el acceso estrictamente al interior de la clase donde se definen. Esta granularidad es mucho más específica que en C, donde la ocultación se gestiona a nivel de unidad de traducción (archivo) mediante la palabra clave static.

Además de los miembros, el modificador public se aplica a la definición de la propia clase de nivel superior (aquella que da nombre al archivo .java). Al declarar una clase como public, se permite que sea importada y utilizada por cualquier otro paquete del programa. Sin embargo, es importante señalar que una clase de nivel superior no puede ser declarada como private (ni protected). El modificador private aplicado a una clase solo es válido en el contexto de clases anidadas (clases definidas dentro de otras clases), donde la clase interna actúa como un miembro más y, por tanto, puede ocultarse del exterior.

Finalmente, debe tenerse en cuenta una restricción importante respecto a C: los modificadores de acceso no se pueden aplicar a variables locales (las declaradas dentro de un método o bloque). Las variables locales en Java tienen una visibilidad determinada exclusivamente por su ámbito léxico (el bloque de llaves { ... } donde nacen), y su ciclo de vida está atado a la ejecución de dicho bloque. Intentar declarar una variable local como private int x; o public int y; generará un error de compilación, ya que el control de acceso entre objetos no tiene sentido dentro de la lógica interna y temporal de una función.

## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En la Programación Orientada a Objetos, la visibilidad no se limita a una dicotomía estricta entre público y privado. Existen niveles intermedios diseñados para facilitar la reutilización de código y la modularidad sin exponer los datos a todo el universo del programa. En Java, se introduce explícitamente el modificador protected, el cual ofrece un nivel de acceso híbrido: permite que los miembros sean accesibles dentro del mismo paquete y, crucialmente, por las subclases (clases que heredan), aunque estas se encuentren en paquetes diferentes. Esto es fundamental para la herencia, permitiendo que las clases hijas accedan a la estructura interna de sus padres sin hacerla pública al resto del mundo.

Adicionalmente, Java posee una característica distintiva conocida como visibilidad de paquete (o package-private), que es el comportamiento por defecto cuando no se escribe ningún modificador de acceso. A diferencia de C++, donde la ausencia de modificador en una class implica privacidad total (private), en Java esto otorga acceso a cualquier clase que resida en el mismo paquete (directorio lógico). Este nivel de visibilidad fomenta la colaboración estrecha entre clases que forman un módulo o librería, actuando de forma similar a cómo las funciones dentro de un mismo archivo .c pueden compartir variables estáticas globales sin exponerlas al resto del programa.

En otros lenguajes como C++, también existe el modificador protected, aunque su funcionamiento se centra más estrictamente en la jerarquía de herencia y no otorga acceso automático a otras clases "vecinas" a menos que se utilice el mecanismo de friend. La declaración friend en C++ es una excepción controlada a la encapsulación que permite a funciones o clases externas específicas acceder a miembros privados, una característica que Java decidió no incluir para forzar un diseño más estructurado. Por otro lado, lenguajes como C# utilizan internal, que restringe la visibilidad al componente compilado (ensamblado o DLL) actual, similar conceptualmente al paquete de Java pero aplicado a la unidad de despliegue.

## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para: (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta

## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento

## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`?

## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase

## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java?

## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?

## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un tipo de dato enumerado en la Programación Orientada a Objetos se define como un tipo especial utilizado para definir colecciones de constantes fijas y con nombre. A diferencia de lo que ocurre en C, donde un enum es poco más que una serie de alias para valores enteros (int) que el compilador apenas distingue de cualquier otro número, en Java un tipo enumerado (enum) es, efectivamente, una clase. De hecho, todos los enumerados heredan implícitamente de la clase base java.lang.Enum. Esto implica que las constantes definidas no son simples enteros, sino instancias (objetos) de esa clase enumerada, poseyendo todas las capacidades asociadas a los objetos.

En términos de encapsulación, la ventaja más evidente frente al enfoque de C es la seguridad de tipos (type safety). Al definir un método que recibe un enumerado como parámetro, se garantiza en tiempo de compilación que solo se podrán pasar los valores predefinidos en dicho tipo, rechazando cualquier otro valor. Esto elimina la necesidad de validar si un número entero recibido corresponde a una opción válida, un error muy común en la programación estructurada cuando se usan macros (#define) o enteros para representar estados u opciones. La integridad de los datos queda protegida al restringir el dominio de valores posibles exclusivamente a los declarados dentro del tipo.

Además, dado que en Java los enumerados son clases, estos permiten una encapsulación rica de datos y comportamiento. Se pueden definir atributos de instancia (preferiblemente private) y métodos para manipularlos dentro del propio enumerado. Por ejemplo, un enumerado Planeta podría encapsular datos como la masa y el radio, y exponer métodos para calcular la gravedad superficial. El constructor de un enumerado es siempre privado o de paquete, lo que impide que código externo instancie nuevos objetos de este tipo; se asegura así que las únicas instancias existentes en memoria sean las constantes definidas, manteniendo un control estricto sobre el estado global del sistema en lo referente a ese tipo de dato.

```java
// Ejemplo ilustrativo de Enum como Clase con encapsulación

public enum NivelAcceso {
    // Definición de las instancias constantes
    INVITADO(1),
    USUARIO(2),
    ADMINISTRADOR(3);

    // Atributo encapsulado (estado interno)
    private final int codigoNivel;

    // Constructor privado (solo invocado internamente por las constantes)
    private NivelAcceso(int codigoNivel) {
        this.codigoNivel = codigoNivel;
    }

    // Método público (accesor) para acceder al dato encapsulado
    public int getCodigoNivel() {
        return codigoNivel;
    }
}
```

## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado

Para implementar este tipo de dato en Java, se debe concebir el enumerado Mes como una clase que encapsula información inmutable. A diferencia de C, donde se necesitarían arreglos paralelos o estructuras externas para asociar el nombre del mes con sus días y su posición, en Java se definen estos datos como atributos propios de cada constante. Al declarar las instancias (ENERO, FEBRERO, etc.), se invoca automáticamente al constructor privado, pasando los valores específicos para el número de días y el orden numérico, garantizando que cada mes nazca con su estado correctamente inicializado.

Se utilizan modificadores de acceso private y final para los atributos numeroDias y orden. El uso de final es crucial en este contexto para asegurar la inmutabilidad: un mes no debe cambiar su posición en el año ni su duración estándar durante la ejecución del programa. Esto refuerza el concepto de encapsulación, ya que los datos están protegidos dentro del objeto y solo son accesibles mediante los métodos públicos (getters), impidiendo cualquier modificación accidental desde el exterior.

A continuación se presenta el código que define el enumerado. Cabe notar que, aunque Java proporciona un método nativo ordinal() (que devuelve índices de 0 a 11), se ha optado por un atributo explícito para el número de mes (1 a 12) para cumplir estrictamente con el requerimiento de usar atributos privados para modelar el estado del objeto. Para el caso de febrero, se establece una duración estándar de 28 días, dado que el cálculo de años bisiestos requeriría lógica externa o pasar el año como parámetro al método, lo cual excede la definición básica de atributos constantes.

```java
        public enum Mes {
            // Definición de las instancias llamando al constructor privado
            ENERO(31, 1),
            FEBRERO(28, 2),
            MARZO(31, 3),
            ABRIL(30, 4),
            MAYO(31, 5),
            JUNIO(30, 6),
            JULIO(31, 7),
            AGOSTO(31, 8),
            SEPTIEMBRE(30, 9),
            OCTUBRE(31, 10),
            NOVIEMBRE(30, 11),
            DICIEMBRE(31, 12);

            // Atributos privados encapsulados (Estado del objeto)
            private final int numeroDias;
            private final int orden;

            // Constructor privado: Se ejecuta una vez por cada constante definida arriba.
            // En Java, los constructores de enum son siempre privados implícitamente.
            private Mes(int numeroDias, int orden) {
                this.numeroDias = numeroDias;
                this.orden = orden;
            }

            // Métodos públicos para acceder a la información (Getters)
            public int getNumeroDias() {
                return this.numeroDias;
            }

            public int getOrden() {
                return this.orden;
            }
        }
```

## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

Para resolver este requerimiento, se dota al enumerado de comportamiento lógico ("métodos de negocio") que opera sobre su propio estado. A diferencia del enfoque procedimental en C, donde se escribirían funciones externas que reciben un mes y un hemisferio para calcular el resultado, aquí es el propio objeto Mes el que sabe responder a qué estación pertenece. Esto mantiene la cohesión de la clase y evita dispersar la lógica condicional por el resto de la aplicación.

La lógica implementada debe tener en cuenta que las estaciones astronómicas no coinciden exactamente con los límites de los meses, y que se invierten según el hemisferio. Al solicitarse si el mes tiene "algunos días" de dicha estación, los meses en los que ocurren solsticios y equinoccios (marzo, junio, septiembre y diciembre) deben considerarse parte de dos estaciones consecutivas. Por ejemplo, junio contiene días de primavera (antes del 21) y de verano (después del 21) en el hemisferio norte.

El parámetro booleano enHemisferioNorte actúa como un inversor de la lógica. Gracias a la encapsulación, quien use esta clase no necesita saber qué meses específicos componen el invierno en el sur; simplemente invoca el método esDeInvierno(false) sobre la instancia del mes y obtiene la respuesta correcta. A continuación se presenta la clase completa con los nuevos métodos integrados.

```java
        public enum Mes {
            ENERO(31, 1),
            FEBRERO(28, 2),
            MARZO(31, 3),
            ABRIL(30, 4),
            MAYO(31, 5),
            JUNIO(30, 6),
            JULIO(31, 7),
            AGOSTO(31, 8),
            SEPTIEMBRE(30, 9),
            OCTUBRE(31, 10),
            NOVIEMBRE(30, 11),
            DICIEMBRE(31, 12);

            private final int numeroDias; // 28,30 o 31 dependiendo del mes
            private final int orden; // del 1 al 12

            private Mes(int numeroDias, int orden) { // --> formato del enum: Mes.MES(numeroDias, orden)
                this.numeroDias = numeroDias;
                this.orden = orden;
            }

            public int getNumeroDias() {
                return this.numeroDias;
            }

            public int getOrden() {
                return this.orden;
            }

            // Métodos para determinar la estación (considerando superposición en meses de cambio)
            
            public boolean esDePrimavera(boolean enHemisferioNorte) {
                if (enHemisferioNorte) {
                    // Primavera Norte: Marzo (3) a Junio (6)
                    return orden >= 3 && orden <= 6;
                } else {
                    // Primavera Sur = Otoño Norte: Septiembre (9) a Diciembre (12)
                    return orden >= 9 && orden <= 12;
                }
            }

            public boolean esDeVerano(boolean enHemisferioNorte) {
                if (enHemisferioNorte) {
                    // Verano Norte: Junio (6) a Septiembre (9)
                    return orden >= 6 && orden <= 9;
                } else {
                    // Verano Sur = Invierno Norte: Diciembre (12), Enero (1) a Marzo (3)
                    return orden == 12 || orden <= 3;
                }
            }

            public boolean esDeOtono(boolean enHemisferioNorte) {
                if (enHemisferioNorte) {
                    // Otoño Norte: Septiembre (9) a Diciembre (12)
                    return orden >= 9 && orden <= 12;
                } else {
                    // Otoño Sur = Primavera Norte: Marzo (3) a Junio (6)
                    return orden >= 3 && orden <= 6;
                }
            }

            public boolean esDeInvierno(boolean enHemisferioNorte) {
                if (enHemisferioNorte) {
                    // Invierno Norte: Diciembre (12), Enero (1) a Marzo (3)
                    return orden == 12 || orden <= 3;
                } else {
                    // Invierno Sur = Verano Norte: Junio (6) a Septiembre (9)
                    return orden >= 6 && orden <= 9;
                }
            }
        }
```
