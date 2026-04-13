<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición

## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea

En el lenguaje C, la agrupación de datos primitivos para construir entidades conceptualmente más complejas se logra de manera natural mediante el uso de `struct`. Este mecanismo permite definir tipos de datos personalizados, como un punto en un plano cartesiano, encapsulando sus dimensiones lógicas. Al incluir estas estructuras como miembros de otra estructura superior, se establece la relación "tiene-un" (*has-a*), lo que constituye la base de la composición en el paradigma estructurado.

Para el caso específico de una línea, su diseño no requiere declarar cuatro variables independientes para las coordenadas, sino que se define de forma semántica componiéndola de dos estructuras de tipo punto (un origen y un destino). Esta abstracción modular facilita enormemente el paso de parámetros a las funciones matemáticas. Por ejemplo, la distancia Euclidiana ($d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$) se calcula mediante una función que recibe dos puntos completos, y la longitud de la línea se obtiene simplemente extrayendo los puntos que la componen y delegando el cálculo a la primera función.

A diferencia del paradigma orientado a objetos que se utiliza en Java, este enfoque de composición en C es puramente estructural y agrupa únicamente los datos. No se asocia el comportamiento (las funciones matemáticas) directamente a las estructuras, ni se cuenta con mecanismos de ocultación de información. En consecuencia, cualquier segmento del código puede acceder y alterar directamente los valores `x` e `y` de los puntos dentro de la línea, evidenciando la ausencia de una encapsulación estricta que proteja el estado de la entidad geométrica.

```c
#include <stdio.h>
#include <math.h>

// Definición del componente básico (el Punto)
struct Punto {
    double x;
    double y;
};

// Composición: La estructura Linea "tiene" dos estructuras Punto
struct Linea {
    struct Punto origen;
    struct Punto destino;
};

// Función matemática que opera sobre la estructura base
double calcularDistancia(struct Punto p1, struct Punto p2) {
    return sqrt(pow(p2.x - p1.x, 2) + pow(p2.y - p1.y, 2));
}

// Función que opera sobre la estructura compuesta, 
// delegando el trabajo a la función de nivel inferior
double calcularLongitudLinea(struct Linea l) {
    return calcularDistancia(l.origen, l.destino);
}

int main() {
    struct Punto p1 = {0.0, 0.0};
    struct Punto p2 = {3.0, 4.0};
    
    struct Linea miLinea = {p1, p2};
    
    double longitud = calcularLongitudLinea(miLinea);
    printf("La longitud de la linea es: %.2f\n", longitud);
    
    return 0;
}
```

## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea

En el paradigma orientado a objetos de Java, la composición se fortalece al unir el estado y el comportamiento dentro de la misma entidad, al tiempo que se establecen barreras estrictas de acceso. Para lograr la inmutabilidad solicitada y proteger la información, los atributos de las clases se declaran con los modificadores `private` y `final`. El primero oculta los datos al exterior (encapsulación), mientras que el segundo garantiza que, una vez asignado un valor mediante el constructor, este jamás pueda ser alterado. De esta manera, un objeto `Punto` nace con coordenadas fijas, y un objeto `Linea` queda anclado permanentemente a los dos puntos que lo definen.

El diseño del comportamiento también experimenta un cambio fundamental respecto a C. En lugar de utilizar funciones globales desconectadas de los datos, las operaciones matemáticas se integran directamente en las clases. La clase `Punto` asume la responsabilidad de calcular la distancia hacia otro punto que se le pase como argumento. Por su parte, la clase `Linea`, que ejemplifica la composición al contener dos instancias de `Punto` como atributos propios, calcula su longitud delegando la operación al método interno de uno de sus componentes.

Este diseño produce un código mucho más modular y seguro. Al omitir deliberadamente la creación de métodos modificadores (comúnmente conocidos como *setters*), se elimina el riesgo de efectos secundarios o alteraciones accidentales. Además, al instanciar la composición, se pueden aprovechar las excepciones vistas previamente en Java para asegurar que una línea no se construya con referencias vacías, garantizando así la integridad del sistema desde el momento de su creación.

```java
class Punto {
    // Atributos inmutables gracias a private y final
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // El comportamiento pertenece al objeto que posee los datos
    public double calcularDistancia(Punto otro) {
        if (otro == null) {
            throw new IllegalArgumentException("El punto de destino no puede ser nulo.");
        }
        return Math.sqrt(Math.pow(otro.x - this.x, 2) + Math.pow(otro.y - this.y, 2));
    }
}

class Linea {
    // Composición: Linea está compuesta por dos objetos Punto inmutables
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Una línea requiere dos puntos válidos.");
        }
        this.origen = origen;
        this.destino = destino;
    }

    // Se delega el cálculo al objeto Punto interno
    public double calcularLongitud() {
        return origen.calcularDistancia(destino);
    }
}
```

## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`

La multiplicidad en el contexto del diseño orientado a objetos define la cantidad de instancias de una clase que se asocian o vinculan con una única instancia de otra clase. Se trata de una regla de cardinalidad que especifica los límites numéricos (mínimos y máximos) permitidos en una relación. Comprender esta métrica es fundamental al momento de programar, ya que determina cómo se debe estructurar la memoria internamente; por ejemplo, una multiplicidad singular se resuelve con una variable de referencia simple, mientras que una multiplicidad múltiple suele requerir el uso de arreglos genéricos o dinámicos (similares a los punteros a bloques de memoria en C).

Al analizar la relación desde la clase `Linea` hacia la clase `Punto` en el diseño planteado, se observa una multiplicidad estricta, fija e invariable. Cada objeto instanciado de tipo `Linea` requiere obligatoriamente y posee exactamente dos objetos de tipo `Punto` (el origen y el destino). Por lo tanto, la multiplicidad en esta dirección se define y se expresa como **1 a 2**. La línea no puede ser instanciada con un solo punto ni está diseñada para almacenar un tercero; esta restricción estructural queda blindada por el diseño de su constructor.

Por otro lado, al evaluar la dirección inversa, desde la clase `Punto` hacia la clase `Linea`, el escenario cambia debido a la forma en que Java maneja las referencias a los objetos. Dado que la clase `Punto` no contiene ningún atributo de tipo `Linea` (es decir, el punto "desconoce" quién lo contiene), y los puntos se pasan como parámetros al constructor, un mismo objeto `Punto` creado en memoria podría ser utilizado para construir múltiples líneas diferentes. Por consiguiente, la multiplicidad en esta dirección se expresa como **1 a muchos (0..*)**, lo que significa que un punto específico puede existir sin pertenecer a ninguna línea, ser parte de una sola, o estar referenciado por múltiples líneas simultáneamente.

## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente

## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no

## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor

## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?
