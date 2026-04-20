<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

El polimorfismo es la capacidad de un mismo mensaje (llamada a un método) de producir comportamientos diferentes según el tipo de objeto que lo reciba. Esta característica es fundamental en la programación orientada a objetos porque permite escribir código más flexible y reutilizable: se pueden manejar objetos de diferentes tipos a través de referencias a su clase base, sin necesidad de conocer exactamente qué tipo concreto es cada uno en tiempo de ejecución.

La sobreescritura (overriding) de métodos es el mecanismo concreto mediante el cual se implementa el polimorfismo. Cuando una clase derivada redefine un método heredado de su clase base, la sobreescritura permite que cada subclase proporcione su propia implementación específica de ese método. A diferencia de C/C++ donde se requiere usar punteros a funciones virtuales explícitamente, en Java la sobreescritura es el comportamiento por defecto para métodos no estáticos ni privados. Esto significa que cuando se llama a un método a través de una referencia a la clase base, se ejecuta la versión correspondiente al tipo real del objeto en tiempo de ejecución.

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python

La ligadura dinámica o enlace tardío es el mecanismo mediante el cual se determina qué método invocar durante la ejecución del programa (en tiempo de ejecución), no durante la compilación (tiempo de compilación). Esta es la base técnica que permite que el polimorfismo funcione: cuando se llama a un método a través de una referencia, el sistema debe decidir en tiempo de ejecución qué implementación específica ejecutar basándose en el tipo real del objeto, no en el tipo de la referencia.

En C++, la ligadura dinámica no es el comportamiento por defecto y debe solicitarse explícitamente usando la palabra clave `virtual`. Sin esta palabra clave, C++ realiza ligadura estática (en tiempo de compilación), lo cual era una decisión de diseño del lenguaje. En Java, sin embargo, la ligadura dinámica es el comportamiento predeterminado para todos los métodos de instancia, lo que significa que no se debe escribir nada especial: el polimorfismo funciona automáticamente. En Python, como en Java, la ligadura dinámica es el comportamiento por defecto; Python usa un sistema de búsqueda de métodos que examina primero la clase del objeto y luego sus clases base en tiempo de ejecución.

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`

```java
class Soldado {
    public void saludar() {
        System.out.println("Soldado saludando de forma estándar.");
    }
}

class Zapador extends Soldado {
    @Override
    public void saludar() {
        System.out.println("¡Zapador presente! Listos para excavar.");
    }
}

class Artillero extends Soldado {
    // Hereda el método saludar de Soldado
}

public class Polimorfismo {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[3];
        ejercito[0] = new Soldado();
        ejercito[1] = new Zapador();
        ejercito[2] = new Artillero();
        
        for (Soldado soldado : ejercito) {
            soldado.saludar();
        }
    }
}
```

En el ejemplo anterior, se crea un array de referencias de tipo `Soldado` que contiene instancias de diferentes tipos: `Soldado`, `Zapador` y `Artillero`. Cuando se itera sobre el array y se llama al método `saludar()`, Java utiliza ligadura dinámica para ejecutar la versión correcta del método según el tipo real de cada objeto. El `Zapador` muestra su propia versión del saludo, mientras que el `Artillero` utiliza la versión heredada de `Soldado`. Este es el polimorfismo en acción: el código no necesita saber exactamente qué tipo de `Soldado` es cada elemento del array; simplemente llama `saludar()` y cada objeto responde de acuerdo a su naturaleza.

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

```java
class Zapador extends Soldado {
    @Override
    public void saludar() {
        super.saludar();  // Invoca el método de la clase base
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}
```

Sí, es posible y muy común invocar el método de la clase base desde una clase derivada utilizando la palabra clave `super`. Esta palabra clave proporciona una referencia explícita a la clase base, permitiendo acceder tanto a métodos como a atributos heredados que puedan haber sido sobreescritos. En el ejemplo anterior, `super.saludar()` ejecuta primero el comportamiento original del `Soldado`, y luego se añade el comportamiento específico del `Zapador`. Esta técnica es especialmente útil cuando se desea extender el comportamiento de un método en lugar de reemplazarlo completamente. La palabra clave `super` es el equivalente en Java a llamar explícitamente a la función base en C++ (por ejemplo, `Soldado::saludar()`).

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

Al sobreescribir un método, los tipos de los parámetros deben ser exactamente idénticos (no puede haber variación en los parámetros); de lo contrario, Java consideraría que es una sobrecarga, no una sobreescritura. Sin embargo, el tipo de retorno puede ser más específico (covarianza): si la clase base retorna un tipo `X`, la subclase puede retornar un tipo que sea una subclase de `X`. Los modificadores de acceso tampoco pueden ser más restrictivos (por ejemplo, no se puede cambiar de `public` a `private`).

La sobreescritura (overriding) ocurre cuando una subclase redefine un método heredado con la misma firma (nombre y parámetros), permitiendo diferentes comportamientos según el tipo de objeto. La sobrecarga (overloading), en cambio, es cuando se tienen múltiples métodos con el mismo nombre pero diferentes parámetros dentro de la misma clase o en la jerarquía de herencia; esto no está relacionado con el polimorfismo de tipos, sino que es una forma de polimorfismo de compilación.

La anotación `@Override` es una anotación que indica explícitamente que se intenta sobreescribir un método de la clase base. Aunque no es obligatoria, es altamente recomendable usarla siempre porque: (1) mejora la legibilidad del código, (2) permite al compilador detectar errores si accidentalmente no coincide la firma del método (por ejemplo, si se cambia un parámetro), y (3) hace que la intención del programador sea clara a otros desarrolladores.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

Sí, exactamente. Desde el momento en que se sobreescribe cualquier método heredado, se está utilizando polimorfismo, aunque inicialmente no sea evidente. Toda clase en Java hereda de la clase `Object`, que proporciona métodos como `toString()`, `equals()`, `hashCode()` y otros. Cuando se sobreescribe `toString()` en una clase personalizada, se está participando en el polimorfismo: si alguien llama a `toString()` a través de una referencia de tipo `Object`, el sistema ejecutará la versión específica de la subclase.

Un ejemplo práctico es cuando se usa `System.out.println(miObjeto)`: internamente, Java invoca `miObjeto.toString()` usando ligadura dinámica, por lo que se ejecuta la versión sobreescrita si existe. De manera similar, cuando se sobrescribe `equals()` para comparar objetos de forma personalizada, se participa en el mecanismo polimórfico: colecciones como `ArrayList` pueden comparar elementos de diferentes tipos usando `equals()`, sin necesidad de conocer el tipo exacto. Por lo tanto, el polimorfismo es fundamental en Java desde el inicio, aunque frecuentemente no se etiquete explícitamente en los primeros temas.

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

```java
abstract class Soldado {
    public void saludar() {
        System.out.println("Soldado saludando.");
    }
    
    abstract void atacar();  // Método abstracto
}

class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Zapador excavando trincheras.");
    }
}

class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Artillero disparando cañones.");
    }
}
```

Una clase abstracta es una clase que no puede ser instanciada directamente; representa un concepto general que define la interfaz común para sus subclases. Se declara usando la palabra clave `abstract` antes de la palabra clave `class`. Un método abstracto es un método que solo tiene declaración pero no implementación; obliga a todas las subclases concretas a proporcionar una implementación específica. Para crear una clase abstracta, se coloca `abstract` antes de `class`; para crear un método abstracto, se coloca `abstract` antes del tipo de retorno, y el método termina con un punto y coma sin cuerpo. No se puede crear instancias de una clase abstracta (intentarlo causará un error de compilación), pero se pueden usar referencias a la clase abstracta para referenciar objetos de sus subclases concretas, proporcionando así un nivel adicional de abstracción en la jerarquía de herencia.

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

La palabra clave `final` restringe la capacidad de sobreescritura y herencia. Cuando se aplica `final` a un método, impide que las subclases lo sobreescriban, desactivando el polimorfismo para ese método específico. Cuando se aplica `final` a una clase, impide que cualquier otra clase la extienda, desactivando completamente la herencia para esa clase. Esto se relaciona directamente con el polimorfismo porque limita o elimina explícitamente la flexibilidad que el polimorfismo proporciona.

Los desarrolladores utilizan `final` generalmente por razones de seguridad, rendimiento o diseño: previene que subclases rompan invariantes importantes, permite que el compilador y la máquina virtual Java optimicen mejor el código (porque saben que el método no será sobreescrito), o simplemente comunica que una clase o método no fue diseñado para ser extendido. Ejemplos de clases `final` en la API estándar de Java incluyen `String`, `Integer`, `Double` y otras clases de envolvimiento de tipos primitivos (wrapper classes), así como `System` y `Math`. Estas clases son `final` para garantizar que su comportamiento no sea alterado accidentalmente y para permitir optimizaciones específicas.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

Una interfaz es un contrato que especifica un conjunto de métodos que una clase debe implementar. A diferencia de las clases (abstractas o no), una interfaz no proporciona una implementación por defecto para sus métodos (aunque a partir de Java 8 pueden tener métodos con implementación `default` y métodos estáticos). Las interfaces se declaran con la palabra clave `interface`, y las clases las implementan con la palabra clave `implements`. Aunque las interfaces y las clases abstractas son conceptualmente similares y pueden parecer intercambiables en algunos casos, tienen diferencias importantes: una clase abstracta puede contener campos con estado y métodos con implementación, mientras que una interfaz es más pura y representa un comportamiento que múltiples clases pueden compartir sin estar relacionadas por herencia.

Sí, una clase puede implementar múltiples interfaces, lo cual es una gran ventaja sobre la herencia simple (Java solo permite heredar de una clase pero implementar muchas interfaces). Esta es la forma en que Java simula la herencia múltiple: en lugar de heredar de varias clases (lo cual complicaría el diseño), una clase puede implementar múltiples interfaces y heredar de una única clase base. Por ejemplo, una clase podría implementar las interfaces `Comparable`, `Cloneable` y `Serializable` simultáneamente, cada una proporcionando un contrato diferente que la clase debe cumplir.

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce)

```java
abstract class Punto {
    abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    double x, y;
    
    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto2D) {
            Punto2D p = (Punto2D) otro;  // Downcasting
            return Math.sqrt((this.x - p.x) * (this.x - p.x) + 
                           (this.y - p.y) * (this.y - p.y));
        }
        throw new IllegalArgumentException("Ambos puntos deben ser 2D");
    }
}

class Punto3D extends Punto {
    double x, y, z;
    
    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    
    @Override
    double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto3D) {
            Punto3D p = (Punto3D) otro;  // Downcasting
            return Math.sqrt((this.x - p.x) * (this.x - p.x) + 
                           (this.y - p.y) * (this.y - p.y) +
                           (this.z - p.z) * (this.z - p.z));
        }
        throw new IllegalArgumentException("Ambos puntos deben ser 3D");
    }
}

class Linea {
    Punto inicio, fin;
    
    public Linea(Punto inicio, Punto fin) {
        this.inicio = inicio;
        this.fin = fin;
    }
    
    double obtenerLongitud() {
        return inicio.calcularDistanciaA(fin);
    }
}
```

En este ejemplo, se usa polimorfismo para que la clase `Linea` no necesite conocer si sus puntos son 2D o 3D: simplemente llama a `calcularDistanciaA()` a través de la referencia de tipo `Punto`, y la ligadura dinámica garantiza que se ejecute la versión correcta. Dentro de cada implementación, se utiliza `instanceof` para verificar que el otro punto es del tipo compatible (ambos 2D o ambos 3D) antes de hacer el downcasting, que es necesario para acceder a los campos específicos (x, y, z) que la clase abstracta no expone. Este patrón es muy potente porque permite escribir código genérico que funciona con cualquier tipo de punto, mientras que internamente cada tipo calcula su distancia según sus propias dimensiones.

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero

```java
interface Fichero {
    String leer();
}

interface FicheroEscribible extends Fichero {
    void escribir(String contenido);
    void eliminar();
}

class FicheroTexto implements FicheroEscribible {
    private String contenido = "";
    
    @Override
    public String leer() {
        return contenido;
    }
    
    @Override
    public void escribir(String contenido) {
        this.contenido = contenido;
    }
    
    @Override
    public void eliminar() {
        this.contenido = "";
    }
}
```

La herencia de interfaces es un mecanismo que permite que una interfaz extienda otra interfaz usando la palabra clave `extends`, de manera similar a como las clases heredan de otras clases. Cuando una interfaz hereda de otra, automáticamente incluye todos los métodos de la interfaz base; así, cualquier clase que implemente la interfaz derivada debe proporcionar implementación para todos los métodos heredados más los nuevos. Esto permite crear jerarquías de interfaces que especifican conjuntos cada vez más complejos de comportamientos.

Sí, existe herencia múltiple de interfaces, y es uno de los puntos donde las interfaces son más flexibles que las clases abstractas. Una interfaz puede extender múltiples interfaces simultáneamente (por ejemplo, `interface MiInterfaz extends Interface1, Interface2, Interface3`), y una clase puede implementar múltiples interfaces que a su vez heredan de otras interfaces. Esto es seguro en Java porque las interfaces no tienen estado (en el sentido tradicional), solo contratos sobre métodos. Esta característica permite diseños muy expresivos: por ejemplo, una clase podría implementar una interfaz que hereda tanto de `Fichero` como de `Sincronizable`, asegurando que implementa todos los métodos necesarios para ser un fichero y para ser sincronizable.
