<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero

Respuesta:

Un puntero a función es una variable que almacena la dirección de una función, permitiendo llamar a esa función indirectamente o pasarla como parámetro. En C, los punteros a función solo referencian el código de la función; no capturan contexto ni estado local.

El siguiente ejemplo define una función que recibe una cadena y devuelve una nueva cadena en mayúsculas, crea un puntero a esa función llamado `aMayusculas` y la invoca a través del puntero.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

char *a_mayusculas_impl(const char *s) {
 if (!s) return NULL;
 char *r = strdup(s);
 if (!r) return NULL;
 for (char *p = r; *p; ++p) *p = toupper((unsigned char)*p);
 return r; // el llamador debe liberar
}

int main(void) {
 const char *texto = "Hola Mundo";
 char *(*aMayusculas)(const char *) = a_mayusculas_impl; // puntero a función
 char *res = aMayusculas(texto); // invocación mediante el puntero
 if (res) {
  puts(res);
  free(res);
 }
 return 0;
}
```

## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda

Respuesta:

Una función lambda es una función anónima, compacta y tratada como valor; se puede asignar a variables, pasar como argumento o devolver. Las lambdas suelen poder cerrar (capturar) variables del entorno donde se definen, lo que las diferencia de los simples punteros a función.

Ejemplo en JavaScript usando una arrow function y asignándola a `aMayusculas`:

```javascript
const aMayusculas = s => s ? s.toUpperCase() : s;
console.log(aMayusculas('Hola Mundo'));
```

Ejemplo en Java usando `Function<String,String>`:

```java
import java.util.function.Function;

public class Ejemplo {
 public static void main(String[] args) {
  Function<String, String> aMayusculas = s -> s == null ? null : s.toUpperCase();
  System.out.println(aMayusculas.apply("Hola Mundo"));
 }
}
```

## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

Respuesta:

El paradigma funcional es un estilo de programación centrado en el uso de funciones puras (sin efectos laterales), composición de funciones, inmutabilidad y evaluación de expresiones. Se prioriza transformar datos mediante funciones y evitar estado mutable compartido.

Lenguajes como Java 8 se denominan multi-paradigma porque incorporan características funcionales (lambdas, streams, APIs funcionales) manteniendo la orientación a objetos tradicional; así se permite elegir la técnica más adecuada para cada problema.

Decir que las funciones son "ciudadanos de primera clase" significa que las funciones pueden ser almacenadas en variables, pasadas como argumentos, devueltas por otras funciones y manipuladas como cualquier otro valor; esto habilita patrones como callbacks, higher-order functions y composición.

## 4. Explica la sintaxis básica de una función lambda en Java

Respuesta:

La sintaxis básica de una lambda en Java es `(param1, param2) -> expresión` o `(param1, param2) -> { /* bloque */ }`. El compilador infiere el tipo de los parámetros a partir del contexto (la interfaz funcional esperada).

Si la lambda contiene una única expresión, esa expresión se evalúa y su resultado se devuelve implícitamente; para varias sentencias se usa un bloque con `return` explícito cuando sea necesario. Las lambdas se usan donde se espera una interfaz funcional, por ejemplo `Function`, `Consumer` o `Predicate`.

## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro

Respuesta:

En JavaScript se puede escribir una función `transformar` que reciba la cadena y la función transformadora y la invoque directamente: esto demuestra higher-order functions.

```javascript
function transformar(s, fn) {
  return fn(s);
}

const aMayusculas = s => s ? s.toUpperCase() : s;
console.log(transformar('hola', aMayusculas));
```

En Java, se puede definir un método estático que reciba `Function<String,String>` y aplique `apply`:

```java
import java.util.function.Function;

public class EjemploTransformar {
 public static String transformar(String s, Function<String,String> fn) {
  return fn.apply(s);
 }

 public static void main(String[] args) {
  Function<String,String> aMayusculas = str -> str == null ? null : str.toUpperCase();
  System.out.println(transformar("hola", aMayusculas));
 }
}
```

## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro

Respuesta:

Es habitual crear la función inline al llamar a `transformar`. En JavaScript se pasa una arrow function que invierte la cadena usando métodos de `String` y `Array`.

```javascript
console.log(transformar('hola', s => s.split('').reverse().join('')));
```

En Java se puede pasar directamente la lambda que invierte la cadena con `StringBuilder`:

```java
System.out.println(transformar("hola", s -> new StringBuilder(s).reverse().toString()));
```

## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda

Respuesta:

Un cierre (closure) es una función junto con el entorno léxico que captura las variables accesibles en el momento de su definición. Las lambdas pueden referirse a variables externas siempre que cumplan la restricción de ser `final` o "effectively final" en Java.

Ejemplo en Java donde la lambda concatena un sufijo capturado desde el contexto:

```java
import java.util.function.Function;

public class ClosureEjemplo {
 public static void main(String[] args) {
  String sufijo = "_SUF"; // efectivamente final
  Function<String,String> añadirSufijo = s -> s + sufijo;
  System.out.println(añadirSufijo.apply("valor")); // imprime "valor_SUF"
 }
}
```

En este ejemplo `añadirSufijo` captura `sufijo` del entorno exterior; si se intentara reasignar `sufijo` después de la definición, el compilador daría error porque debe permanecer efectivamente final.

## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

Respuesta:

Las diferencias principales se centran en captura de estado y tipo. Las lambdas pueden ser closures y capturar variables del entorno, los punteros a función en C solo apuntan al código y no pueden capturar variables locales.

Además, las lambdas están integradas en el sistema de tipos del lenguaje (con interfaces funcionales en Java) y disponen de inferencia y seguridad de tipos mayor; los punteros en C son más rudimentarios y propensos a errores de firma y gestión de memoria.

## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento

Respuesta:

Se puede implementar `crearDescuento` que capture el `porcentaje` y devuelva una lambda que aplica ese porcentaje a una cantidad; así se demuestra que la función devuelta retiene el valor del entorno (closure).

```java
import java.util.function.Function;

public class Descuento {
 public static Function<Double, Double> crearDescuento(double porcentaje) {
  return cantidad -> cantidad * (1.0 - porcentaje / 100.0);
 }

 public static void main(String[] args) {
  Function<Double, Double> descuento10 = crearDescuento(10);
  Function<Double, Double> descuento25 = crearDescuento(25);
  System.out.println(descuento10.apply(100.0)); // 90.0
  System.out.println(descuento25.apply(100.0)); // 75.0
 }
}
```

La closure ocurre porque cada lambda devuelta captura su propio `porcentaje` en el momento de creación; aunque la función `crearDescuento` finalice, las lambdas conservan el valor capturado.

## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

Respuesta:

Una interfaz funcional es una interfaz que declara exactamente un método abstracto (la "single abstract method" o SAM). Se puede anotar con `@FunctionalInterface`, aunque la anotación es opcional; su objetivo es permitir que lambdas y referencias a método se adapten al tipo de la interfaz.

Además del método abstracto único, una interfaz funcional puede contener métodos `default`, `static` y `private` sin romper la condición. Para ser usable con lambdas, el contexto debe esperar una instancia de esa interfaz (por inferencia de tipos).

## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`)

Respuesta:

Se puede declarar la interfaz `Transformador` con un único método `transformar`. Esto permite usar lambdas o referencias a método donde se espere `Transformador`.

```java
@FunctionalInterface
public interface Transformador {
 String transformar(String s);
}

// Uso:
// Transformador t = s -> s.toUpperCase();
// System.out.println(t.transformar("hola"));
```

Esta interfaz permite pasar lambdas como implementaciones en los lugares que la requieran, sin necesidad de crear clases anónimas ni implementaciones concretas.

## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`

Respuesta:

Se puede generalizar `Transformador<T,R>` para representar una transformación de `T` a `R`. Esto es equivalente a `Function<T,R>` en la API estándar.

```java
@FunctionalInterface
public interface Transformador<T,R> {
 R transformar(T t);
}

// Ejemplo de uso:
Transformador<Double,Integer> redondear = d -> (int)Math.round(d);
System.out.println(redondear.transformar(3.7)); // 4
```

El ejemplo anterior muestra la declaración genérica y su uso inmediato para convertir un `Double` en `Integer` mediante redondeo.

## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java

Respuesta:

Java proporciona muchas interfaces funcionales en `java.util.function`. Las más usadas incluyen:

- `Function<T,R>`: transforma `T` en `R`.
- `Consumer<T>`: recibe `T` y no devuelve nada (efecto secundario).
- `Supplier<T>`: no recibe parámetros y suministra `T`.
- `Predicate<T>`: acepta `T` y devuelve `boolean`.
- `UnaryOperator<T>`: `Function<T,T>` especializada.
- `BinaryOperator<T>`: `BiFunction<T,T,T>` especializada.
- `BiFunction<T,U,R>` y `BiConsumer<T,U>`: versiones binarias.

Además existen `IntFunction`, `LongUnaryOperator`, `DoubleBinaryOperator`, etc., para tipos primitivos y mejorar rendimiento evitando autoboxing.

## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo

Respuesta:

`List.forEach` acepta un `Consumer<? super T>` y aplica la acción a cada elemento. Es una forma declarativa de recorrer colecciones cuando solo interesa aplicar una acción por elemento.

A continuación se muestra un ejemplo de uso práctico.

```java
import java.util.Arrays;

public class ForEachEjemplo {
 public static void main(String[] args) {
  Arrays.asList(1, -2, 3, 0, 5).forEach(n -> {
   if (n > 0) System.out.println(n + " es positivo");
  });
 }
}
```

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora

Respuesta:

Se usa `Consumer<? super T>` para aceptar consumidores que procesen supertipos de `T`, aumentando la flexibilidad. `? super T` permite pasar, por ejemplo, un `Consumer<Object>` cuando se tiene una `List<String>`; si se usara `Consumer<T>` esto no sería posible.

PECS es el acrónimo "Producer Extends, Consumer Super": si un parámetro genérico produce valores, usar `extends`; si lo consume, usar `super`. Aplicado a `transformar`, si se quiere aceptar funciones más flexibles se puede declarar `Function<? super T, ? extends R>` para permitir lambdas que acepten subtipos como entrada o devuelvan subtipos del resultado esperado.

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`

Respuesta:

En JavaScript se puede usar `bind` para obtener una referencia ligada al objeto; la referencia se comporta como una función.

```javascript
class Persona {
 constructor(nombre) { this.nombre = nombre; }
 saludar() { console.log('Hola, soy ' + this.nombre); }
}

const p = new Persona('Ana');
const saludarRef = p.saludar.bind(p);
saludarRef();
```

En Java se puede usar una referencia a método de instancia sobre una instancia concreta:

```java
public class Persona {
  private final String nombre;
  public Persona(String nombre) { this.nombre = nombre; }
  public void saludar() { System.out.println("Hola, soy " + nombre); }
  public static void main(String[] args) {
    Persona p = new Persona("Ana");
    Runnable saludarRef = p::saludar; // referencia al método de la instancia
    saludarRef.run();
  }
}
```

## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia

Respuesta:

Java soporta varias formas de referencias a método: referencia a método estático (`Class::staticMethod`), a constructor (`Class::new`), a método de instancia de una instancia concreta (`instance::method`) y a método de instancia sobre cualquier instancia de un tipo (`Class::instanceMethod`).

Ejemplos:

```java
// Estático
Runnable r1ref = MyClass::saludoEstatico;

// Constructor
Supplier<MyClass> s = MyClass::new;

// Método de instancia de una instancia concreta
MyClass m = new MyClass();
Runnable r2 = m::saludar;

// Método de instancia sobre cualquier instancia (ej.: comparar strings)
BiFunction<String,String,Integer> f = String::compareToIgnoreCase;
```

Estos estilos permiten adaptar comportamiento de forma concisa y a menudo reutilizable.

## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`

Respuesta:

Versión con lambda manual:

```java
import java.util.*;

class Persona {
 String nombre; int edad;
 Persona(String n,int e){ nombre=n; edad=e; }
 public String getNombre(){ return nombre; }
 public int getEdad(){ return edad; }
}

List<Persona> lista = new ArrayList<>(List.of(new Persona("Ana",30), new Persona("Luis",25), new Persona("Paz",30)));
Collections.sort(lista, (p1,p2) -> {
 int cmp = Integer.compare(p1.getEdad(), p2.getEdad());
 return cmp != 0 ? cmp : p1.getNombre().compareTo(p2.getNombre());
});
```

Versión con `Comparator` utilitario:

```java
Collections.sort(lista, Comparator.comparingInt(Persona::getEdad)
 .thenComparing(Persona::getNombre));
```

La segunda versión es más expresiva y reutilizable, aprovechando las combinaciones que ofrece `Comparator`.
