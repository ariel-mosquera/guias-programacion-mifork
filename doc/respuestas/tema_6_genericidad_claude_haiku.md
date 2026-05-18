
<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato

En Java, la clase `Object` es la clase raíz de toda la jerarquía de clases, por lo que cualquier objeto puede ser tratado como `Object`. Esto permite crear estructuras de datos genéricas que almacenen referencias a objetos de cualquier tipo sin necesidad de código duplicado.

```java
public class Contenedor {
    private Object[] elementos;
    private int tamaño = 0;
    
    public Contenedor(int capacidad) {
        elementos = new Object[capacidad];
    }
    
    public void agregar(Object obj) {
        if (tamaño < elementos.length) {
            elementos[tamaño++] = obj;
        }
    }
    
    public Object obtener(int indice) {
        return elementos[indice];
    }
}

// Uso
Contenedor contenedor = new Contenedor(5);
contenedor.agregar("Hola");
contenedor.agregar(42);
contenedor.agregar(3.14);
String texto = (String) contenedor.obtener(0);
int numero = (Integer) contenedor.obtener(1);
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica?

La programación genérica es una técnica que permite escribir código que funciona con múltiples tipos de datos manteniendo la seguridad de tipos. El objetivo es evitar duplicar código cuando se necesita la misma lógica para diferentes tipos, permitiendo que sea el compilador quien verifique la compatibilidad de tipos en lugar de dejarla para el tiempo de ejecución.

El ejemplo anterior sí es un ejemplo básico de programación genérica, pero con limitaciones importantes. Utiliza `Object` para aceptar cualquier tipo, lo que funciona a nivel de lógica; sin embargo, carece de seguridad de tipos real. Es una aproximación primitiva porque requiere downcasting explícito al recuperar elementos, sin que el compilador pueda verificar si los tipos son compatibles. Es más seguro en tiempo de compilación delegar esta responsabilidad al compilador mediante mecanismos específicos de generics.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas

El principal problema es que el compilador no puede verificar la seguridad de tipos. Cuando se recupera un elemento de la estructura, no hay garantía de cuál es su tipo real; el programador debe saber (o asumir) cuál es el tipo correcto para poder hacer el downcasting. Si se realiza un downcasting incorrecto (por ejemplo, intentar convertir un `Integer` a `String`), el error no se detecta en tiempo de compilación, sino que resulta en una excepción `ClassCastException` en tiempo de ejecución.

Otro problema significativo es la pérdida de información de tipos. Cada vez que se almacena o recupera un elemento, se pierde información sobre su tipo específico, lo que obliga al uso de casting explícito y hace el código más propenso a errores. Además, en código que mezcla múltiples tipos dentro de la misma estructura, es fácil cometer errores de lógica donde se intenta usar un tipo equivocado sin que el compilador pueda alertar al respecto. Esta falta de chequeo de tipos estático es especialmente problemática en proyectos grandes donde es difícil rastrear qué tipo de objetos se han almacenado en cada estructura.

## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**?

Los parámetros de tipo son variables que representan tipos desconocidos en el momento de escribir el código. Se definen usando nombres entre ángulos, como `<T>`, `<K>`, `<V>`, etc. Actúan como placeholders que se sustituyen por tipos reales cuando se instancia o utiliza la clase o método. Por ejemplo, en `List<T>`, la `T` es un parámetro de tipo que será reemplazado por el tipo concreto cuando se cree una instancia como `List<String>` o `List<Integer>`.

Esta aproximación permite que el compilador verifique la seguridad de tipos de forma estática. Cuando se declara una estructura con parámetros de tipo, se comunica explícitamente qué tipo de datos contendrá, y el compilador se asegura de que solo se almacenen y recuperen elementos del tipo correcto. A diferencia del enfoque con `Object`, donde el compilador permitía cualquier tipo, los parámetros de tipo restringen las operaciones a solo aquellas que sean válidas para el tipo especificado, eliminando la necesidad de downcasting y previniendo errores antes de que el código se ejecute.

## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad

**Java - Generics:**

```java
import java.util.ArrayList;
import java.util.List;

List<String> palabras = new ArrayList<String>();
palabras.add("Hola");
palabras.add("Mundo");
palabras.add("Java");

for (String palabra : palabras) {
    System.out.println(palabra.toUpperCase()); // toUpperCase() es seguro, palabra es String
}

// Esto no compila:
// palabras.add(42); // Error en tiempo de compilación
```

**C++ - Templates:**

```cpp
#include <vector>
#include <iostream>
using namespace std;

int main() {
    vector<string> palabras;
    palabras.push_back("Hola");
    palabras.push_back("Mundo");
    palabras.push_back("C++");
    
    for (const string& palabra : palabras) {
        cout << palabra << endl; // palabra es string, uso seguro
    }
    
    // Esto no compila:
    // palabras.push_back(42); // Error en tiempo de compilación
    
    return 0;
}
```

En ambos casos, el compilador garantiza que solo se almacenan `String`/`string`, por lo que al recuperar elementos no se requiere casting y se pueden usar los métodos específicos de ese tipo de forma segura.

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

Java y C++ utilizan estrategias completamente diferentes para manejar generics/templates. En **C++**, el compilador realiza "instanciación de plantillas": genera una copia completamente nueva de la clase por cada tipo concreto utilizado. Cuando se declara `vector<int>` y `vector<string>`, el compilador crea dos versiones diferentes del código máquina, optimizado específicamente para cada tipo. Esto resulta en código más rápido pero con una mayor cantidad de código compilado.

En **Java**, el compilador utiliza "type erasure" (borrado de tipos): mantiene la información de tipos solo durante la compilación para verificar seguridad, pero la elimina en el bytecode final. Cuando el código se compila, `List<String>` y `List<Integer>` se convierten internamente en `List<Object>`. En tiempo de ejecución, la JVM no sabe si una `List` debía contener `String` o `Integer`; solo el compilador lo sabía. Esto reduce el tamaño del bytecode pero significa que los generics son una característica principalmente de compilación. El type erasure es por qué no se puede hacer `new T()` dentro de una clase genérica o acceder a `T.class` en tiempo de ejecución.

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`

```java
public class Par<T, U> {
    private T primero;
    private U segundo;
    
    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }
    
    public T getPrimero() {
        return primero;
    }
    
    public U getSegundo() {
        return segundo;
    }
}

public class Estadistica {
    public static Par<Double, Double> calcularMediaYDesviacion(double[] valores) {
        double suma = 0;
        for (double v : valores) {
            suma += v;
        }
        double media = suma / valores.length;
        
        double sumaCuadrados = 0;
        for (double v : valores) {
            sumaCuadrados += Math.pow(v - media, 2);
        }
        double desviacion = Math.sqrt(sumaCuadrados / valores.length);
        
        return new Par<>(media, desviacion);
    }
    
    public static void main(String[] args) {
        double[] datos = {1.5, 2.3, 3.1, 2.8, 3.9};
        Par<Double, Double> resultado = calcularMediaYDesviacion(datos);
        
        System.out.println("Media: " + resultado.getPrimero());
        System.out.println("Desviación: " + resultado.getSegundo());
    }
}
```

La clase `Par<T, U>` utiliza dos parámetros de tipo diferentes, permitiendo almacenar dos valores de tipos distintos. En el ejemplo, se utiliza `Par<Double, Double>` para devolver tanto la media como la desviación típica, manteniendo total seguridad de tipos sin necesidad de casting.

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo

**Sin generics (con Object):**

```java
public static Object seleccionaUnoSinGenerics(Object a, Object b) {
    return Math.random() < 0.5 ? a : b; // Retorna Object
}

// Uso
Object resultado = seleccionaUnoSinGenerics("Hola", "Mundo");
String texto = (String) resultado; // Downcasting necesario

// Error no detectado en compilación:
Object mezcla = seleccionaUnoSinGenerics("Hola", 42); // Tipos diferentes, ¡permitido!
String conversion = (String) mezcla; // Podría fallar en ejecución
```

**Con generics (con parámetro de tipo):**

```java
public static <T> T seleccionaUno(T a, T b) {
    return Math.random() < 0.5 ? a : b; // Retorna T
}

// Uso
String resultado = seleccionaUno("Hola", "Mundo"); // Sin casting

// Error detectado en compilación:
// String mezcla = seleccionaUno("Hola", 42); // No compila: tipos incompatibles
```

La diferencia es fundamental: con generics, (i) el tipo de retorno es exactamente el tipo de entrada, eliminando la necesidad de downcasting, y (ii) el compilador obliga a que ambos parámetros sean del mismo tipo, previniendo errores que de otro modo solo se detectarían en ejecución. El método genérico es más seguro, más legible y requiere menos código.

## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

**Solución 1: Sin generics (con Number):**

```java
public class PuntoConNumber {
    private Number x, y;
    
    public PuntoConNumber(Number x, Number y) {
        this.x = x;
        this.y = y;
    }
    
    public Number getX() { return x; }
    public Number getY() { return y; }
    
    public double calcularDistanciaA(PuntoConNumber otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// Uso
PuntoConNumber p1 = new PuntoConNumber(3, 4);
PuntoConNumber p2 = new PuntoConNumber(0.0, 0.0);
Number coordenadaX = p1.getX(); // Retorna Number, necesita casting para usar
```

**Solución 2: Con generics (con bounded type parameter):**

```java
public class Punto<T extends Number> {
    private T x, y;
    
    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }
    
    public T getX() { return x; }
    public T getY() { return y; }
    
    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// Uso
Punto<Integer> p1 = new Punto<>(3, 4);
Punto<Integer> p2 = new Punto<>(0, 0);
Integer coordenadaX = p1.getX(); // Retorna Integer, es el tipo específico

Punto<Double> p3 = new Punto<>(3.5, 4.2);
Double coord = p3.getX(); // Retorna Double
```

La sintaxis `<T extends Number>` es una restricción de tipo (bounded type parameter) que indica que `T` debe ser `Number` o un subtipo suyo. Tras el type erasure de Java, el compilador reemplaza `T` por su cota superior `Number`, por lo que en el bytecode la clase `Punto<T>` se convierte en una clase `Punto` con coordenadas de tipo `Number`. Sin embargo, durante la compilación se mantiene la información de tipos, permitiendo que el compilador verifique seguridad: `Punto<Integer>` garantiza que sus coordenadas serán `Integer`, mientras que con `Number` podrían ser cualquier subtipo de número.

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

La diferencia en seguridad de tipos es sustancial. Con la solución sin generics (`Number`), es totalmente posible crear un `PuntoConNumber` donde una coordenada es `Integer` y la otra es `Double`: `new PuntoConNumber(3, 4.5)`. El compilador permite esto porque ambas son `Number`. Esto puede llevar a comportamientos inesperados o lógica inconsistente, ya que las coordenadas no tienen garantizado ser del mismo tipo numérico.

Con generics, esto es imposible: `new Punto<Integer>(3, 4.5)` no compila porque `4.5` es `Double`, no `Integer`. El compilador obliga a que ambas coordenadas sean exactamente del mismo tipo numérico. Respecto a los getters: `getX()` en la solución sin generics devuelve `Number` (requiere casting para usar métodos específicos), mientras que en la solución con generics devuelve el tipo exacto `T` (en `Punto<Integer>` devuelve `Integer`, en `Punto<Double>` devuelve `Double`). Esto elimina la necesidad de casting y proporciona mayor seguridad y claridad de código.

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting

```java
public interface Punto<T extends Punto<T>> { 
    public double distanciaA(T p); 
} 

public class Punto2D implements Punto<Punto2D> { 
    private final double x, y; 
    
    public Punto2D(double x, double y) { 
        this.x = x; 
        this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto2D p) {
        // Ya sabemos con seguridad que p es Punto2D, sin instanceof
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2)); 
    } 
} 

public class Punto3D implements Punto<Punto3D> { 
    private final double x, y, z; 
    
    public Punto3D(double x, double y, double z) { 
        this.x = x; 
        this.y = y; 
        this.z = z; 
    } 

    @Override 
    public double distanciaA(Punto3D p) {
        // Ya sabemos con seguridad que p es Punto3D, sin instanceof
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2) 
                + Math.pow(z - p.z, 2)); 
    } 
} 

// Uso
Punto2D punto1 = new Punto2D(0, 0);
Punto2D punto2 = new Punto2D(3, 4);
System.out.println(punto1.distanciaA(punto2)); // 5.0
```

Esta solución utiliza el patrón "self-bounded type" (`<T extends Punto<T>>`), que es una técnica avanzada en generics. Garantiza que cada clase implementa la interfaz con su propio tipo: `Punto2D implements Punto<Punto2D>`. De esta forma, el parámetro del método `distanciaA` es del mismo tipo de la clase, permitiendo comparaciones seguras sin necesidad de `instanceof` ni downcasting. El compilador verifica en tiempo de compilación que solo se pasan instancias del tipo correcto, mejorando significativamente la seguridad y legibilidad del código.

## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo

La respuesta es diferente en cada caso. `String[]` sí es subtipo de `Object[]` (arrays son **covariantes**), pero `List<String>` **no** es subtipo de `List<Object>` (los generics en Java son **invariantes**). Para ver por qué, considérese este código problemático con arrays:

```java
String[] strings = {"Hola", "Mundo"};
Object[] objetos = strings; // Permitido (covariante)
objetos[0] = 42; // ¡Compila pero falla en ejecución con ArrayStoreException!
```

El compilador permite asignar `String[]` a `Object[]`, pero en tiempo de ejecución el array sigue siendo de `String`, por lo que intentar almacenar un `Integer` causa una excepción. Con generics, Java previene esto siendo **invariante**: `List<String>` y `List<Object>` son tipos completamente distintos, sin relación de herencia, evitando el problema anterior.

**Definiciones:**

- **Covariante**: Si `T` es subtipo de `U`, entonces `Container<T>` es subtipo de `Container<U>`. Los arrays de Java son covariantes, pero esto permite errores en tiempo de ejecución.
- **Contravariante**: Si `T` es subtipo de `U`, entonces `Container<U>` es subtipo de `Container<T>` (relación inversa). Menos común, útil para parámetros de entrada.
- **Invariante**: `Container<T>` y `Container<U>` no tienen relación de herencia, aunque `T` sea subtipo de `U`. Esto es lo que hace Java con los generics por defecto, siendo más seguro a costa de flexibilidad.

## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`

Un wildcard (`?`) es un parámetro de tipo desconocido que proporciona flexibilidad controlada en los generics. `List<? extends Number>` es una lista que contiene elementos que son `Number` o subtipo de `Number` (covarianza controlada), permitiendo leer datos con seguridad. `List<? super Integer>` es una lista que contiene elementos que son `Integer` o supertipo de `Integer` (contravarianza controlada), permitiendo escribir datos de forma segura.

**Ejemplo 1: Suma con `? extends` (lectura segura):**

```java
public static double sumarNumeros(List<? extends Number> numeros) {
    double suma = 0;
    for (Number n : numeros) {
        suma += n.doubleValue();
    }
    return suma;
}

// Uso flexible:
List<Integer> enteros = Arrays.asList(1, 2, 3);
List<Double> decimales = Arrays.asList(1.5, 2.5, 3.5);
System.out.println(sumarNumeros(enteros));   // 6.0
System.out.println(sumarNumeros(decimales)); // 7.5
```

**Ejemplo 2: Añadir números con `? super` (escritura segura):**

```java
public static void anadirEnteros(List<? super Integer> lista, int... valores) {
    for (int valor : valores) {
        lista.add(valor);
    }
}

// Uso flexible:
List<Number> numeros = new ArrayList<>();
List<Integer> enteros = new ArrayList<>();
anadirEnteros(numeros, 1, 2, 3);  // Compilador sabe que Integer cabe en Number
anadirEnteros(enteros, 4, 5, 6);  // Y también en Integer
```

`List<? extends T>` se usa cuando se necesita leer de la lista (productor, PECS: Producer Extends). `List<? super T>` se usa cuando se necesita escribir en la lista (consumidor, PECS: Consumer Super). Los wildcards restauran flexibilidad que la invarianza de generics había eliminado, permitiendo escribir código más reutilizable manteniendo seguridad de tipos.
