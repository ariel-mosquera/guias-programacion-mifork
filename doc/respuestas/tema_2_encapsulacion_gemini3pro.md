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

### Respuesta

## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información

### Respuesta

## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

### Respuesta

## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

### Respuesta

## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

### Respuesta

## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

### Respuesta

## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

### Respuesta

## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta

### Respuesta

## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta

## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta

## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta

## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta

## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento

### Respuesta

## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`?

### Respuesta

## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase

### Respuesta

## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta

## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta

## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta

## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta

## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java?

### Respuesta

## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?

### Respuesta

## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta

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

### Respuesta

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

### Respuesta

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
