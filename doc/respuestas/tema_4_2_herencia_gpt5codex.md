<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden

La herencia es un mecanismo de modelado en el que una clase más específica reutiliza y extiende otra más general. La frase "A es-un B" significa que un objeto del tipo A puede tratarse como un objeto del tipo B sin perder validez semántica. Por ejemplo, si se afirma que `Artillero` es-un `Soldado`, entonces cualquier lugar del código que espere un `Soldado` puede recibir un `Artillero`.

De ahí salen dos implicaciones principales. La primera es la compatibilidad de tipos: una referencia `Soldado` puede apuntar a instancias de `Soldado`, `Artillero` o `Zapador`. La segunda es la herencia de estado y comportamiento: las subclases incorporan los atributos y métodos heredables de la superclase (como el `nombre` y `saludar()`), y además agregan su parte específica (cohetes o minas).

```java
class Soldado {
 private String nombre;

 public Soldado(String nombre) {
  this.nombre = nombre;
 }

 public void saludar() {
  System.out.println("Soy " + nombre);
 }
}

class Artillero extends Soldado {
 private int cohetes;

 public Artillero(String nombre, int cohetes) {
  super(nombre); // llama al constructor de la super clase (Soldado)
  this.cohetes = cohetes;
 }

 public int getCohetes() {
  return cohetes;
 }
}

class Zapador extends Soldado {
 private int minas;

 public Zapador(String nombre, int minas) {
  super(nombre);
  this.minas = minas;
 }

 public int getMinas() {
  return minas;
 }
}

public class Demo1 {
    public static void pasarRevista(String[] args) {
        Soldado[] peloton = {
            new Artillero("Alonso", 4),
            new Zapador("Bea", 2),
            new Artillero("Carlos", 6)
        };

        for (Soldado s : peloton) {
            s.saludar();
        }
    }
}
```

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre?

Al crear una instancia de una subclase se ejecutan varios constructores, uno por cada nivel de la jerarquía. El orden siempre va de arriba hacia abajo: primero se inicializa la superclase y después la subclase. En el ejemplo, al crear un `Artillero`, se ejecuta primero `Soldado(...)` y luego `Artillero(...)`.

La palabra clave `super` dentro del constructor sirve para invocar explícitamente un constructor de la clase padre. Esa llamada debe estar en la primera línea del constructor. Si la clase base no expone un constructor sin parámetros, no existe posibilidad de omitir la llamada: hay que invocar `super(...)` con los argumentos adecuados de forma obligatoria.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases

Sí, los atributos privados de la superclase forman parte del objeto completo en memoria, también cuando la instancia es de una subclase. Un `Artillero` contiene físicamente la parte de `Soldado` (por ejemplo, `nombre`) y su parte propia (por ejemplo, `cohetes`). Por tanto, no hay dos objetos separados: hay un único objeto con una estructura que incluye ambos niveles.

Sin embargo, que exista en memoria no implica acceso directo desde código de subclase. Si `nombre` es `private` en `Soldado`, el código de `Artillero` o `Zapador` no puede usar `nombre` directamente. Para acceder de forma válida hay que exponer métodos en `Soldado` (por ejemplo, un getter o un método protegido) y respetar la encapsulación.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica

La compatibilidad por supertipo permite escribir código que depende de una abstracción general (`Soldado`) en lugar de depender de cada subtipo concreto. Eso mejora la extensibilidad porque, al añadir nuevas subclases, el código cliente que ya trabaja con `Soldado` puede seguir funcionando sin cambios estructurales.

El beneficio práctico es que se reduce el coste de evolución. Si más adelante aparece un `Francotirador`, basta con crear la nueva clase y colocar instancias en el mismo array. El bucle de saludo no se toca porque ya estaba diseñado para operar sobre el tipo común.

```java
class Francotirador extends Soldado {
 private int balas;

 public Francotirador(String nombre, int balas) {
  super(nombre);
  this.balas = balas;
 }

 public int getBalas() {
  return balas;
 }
}

Soldado[] peloton = {
 new Artillero("Alonso", 4),
 new Zapador("Bea", 2),
 new Francotirador("Dario", 15)
};

for (Soldado s : peloton) {
 s.saludar(); // No se modifica al agregar nuevos tipos.
}
```

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima

Sí, una referencia del supertipo puede apuntar a objetos reales de un subtipo; eso es exactamente la base del polimorfismo. En Java esto suele ocurrir de forma implícita y segura, y se denomina `upcasting` (por ejemplo, asignar un `Artillero` a una variable `Soldado`).

Con una referencia del supertipo solo se pueden invocar, en compilación, los métodos declarados en ese supertipo. Para acceder a operaciones específicas del subtipo hace falta `downcasting`, que es convertir de `Soldado` a `Artillero`. Esa conversión debe hacerse con cuidado y normalmente precedida de `instanceof`, que comprueba el tipo real del objeto en tiempo de ejecución.

```java
Soldado[] peloton = {
 new Artillero("Alonso", 4),
 new Zapador("Bea", 2),
 new Artillero("Carlos", 6)
};

for (Soldado s : peloton) {
 s.saludar();

 if (s instanceof Artillero) {
  Artillero a = (Artillero) s; // downcasting seguro tras instanceof
  System.out.println("Cohetes: " + a.getCohetes());
 }
}
```

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`

El modificador `protected` permite acceso desde la propia clase, desde sus subclases y también desde clases del mismo paquete. Es más abierto que `private`, pero más restrictivo que `public`. Se usa cuando se quiere habilitar extensión en herencia sin exponer totalmente el dato a cualquier cliente externo.

En Java se implementa declarando el miembro con la palabra clave `protected`. En el ejemplo, si `nombre` se marca como protegido, `Zapador` puede reutilizar ese dato para su comportamiento especializado (por ejemplo, informar quién coloca minas) sin necesidad de un getter público.

```java
class Soldado {
 protected String nombre;

 public Soldado(String nombre) {
  this.nombre = nombre;
 }
}

class Zapador extends Soldado {
 private int minas;

 public Zapador(String nombre, int minas) {
  super(nombre);
  this.minas = minas;
 }

 public void ponerMinas() {
  System.out.println(nombre + " ha colocado " + minas + " minas.");
 }
}
```

## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

No todos los lenguajes orientados a objetos imponen una clase raíz universal. Hay lenguajes donde la jerarquía se define de forma más flexible y no necesariamente existe un ancestro único obligatorio para todas las clases. Por eso, no conviene asumir que el modelo de un lenguaje se replica automáticamente en otro.

En Java sí existe una clase base común para tipos de referencia: `Object`. Cualquier clase definida por el usuario hereda, directa o indirectamente, de `Object`. Esto aporta operaciones generales compartidas (`toString()`, `equals()`, `hashCode()`, etc.) y un punto común de tipado para trabajar con colecciones y APIs genéricas.

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La herencia múltiple consiste en permitir que una clase herede implementación y estado de más de una superclase al mismo tiempo. Esto aumenta la capacidad de reutilización, pero también introduce conflictos, por ejemplo cuando dos superclases definen métodos con la misma firma y comportamientos distintos.

Java no permite herencia múltiple de clases. Una clase solo puede extender una clase concreta. Sin embargo, Java sí permite implementar múltiples interfaces, lo que ofrece una forma controlada de combinar contratos de comportamiento. Si hay métodos `default` en conflicto entre interfaces, se obliga a resolver explícitamente en la clase implementadora.

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente

Una excepción no controlada se modela heredando de `RuntimeException`. Esto permite lanzar el error sin obligación de declararlo con `throws` ni capturarlo de forma inmediata. Es útil para fallos de lógica o estados inválidos que no se esperan como parte normal del flujo.

Si además se quiere conservar contexto de dominio, se puede componer la excepción con el objeto implicado, en este caso `Usuario`. De ese modo, al capturar la excepción se conoce exactamente qué usuario produjo el problema. También conviene sobrecargar constructores para guardar opcionalmente la causa original.

```java
class Usuario {
 private final String id;
 private final String nombre;

 public Usuario(String id, String nombre) {
  this.id = id;
  this.nombre = nombre;
 }

 public String getId() { return id; }
 public String getNombre() { return nombre; }
}

class UsuarioNoEncontradoException extends RuntimeException {
 private final Usuario usuario;

 public UsuarioNoEncontradoException(Usuario usuario) {
  super("Usuario no encontrado: " + usuario.getId());
  this.usuario = usuario;
 }

 public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
  super("Usuario no encontrado: " + usuario.getId(), causa);
  this.usuario = usuario;
 }

 public Usuario getUsuario() {
  return usuario;
 }
}
```

## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

La herencia crea un acoplamiento fuerte entre subclase y superclase: se hereda no solo código útil, sino también decisiones de diseño, invariantes y evolución futura de la clase base. Si el motivo real era solo "evitar duplicación", se puede terminar forzando una relación "es-un" que conceptualmente no existe.

Cuando no hay relación semántica clara, la jerarquía se vuelve frágil y difícil de mantener. Cambios en la clase base pueden romper subclases inesperadamente. Por eso, para reutilización pura suele ser mejor extraer comportamiento en componentes y delegar, en lugar de heredar por conveniencia.

## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

La composición permite construir objetos a partir de partes con responsabilidades pequeñas y bien delimitadas. Esto facilita sustituir comportamientos, probarlos por separado y evolucionar el sistema sin depender de jerarquías rígidas. En términos prácticos, una clase "tiene-un" colaborador, en lugar de "ser-un" subtipo forzado.

Además, la composición reduce el impacto de cambios en una clase sobre otras. En herencia, una modificación en la superclase se propaga a toda la cadena. En composición, cada clase controla mejor su contrato público y delega solo lo necesario. Por eso suele considerarse una estrategia más flexible y mantenible.

## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

La idea apunta a que la subclase, para extender correctamente, necesita conocer detalles internos de la superclase más allá de su interfaz pública estricta. En especial cuando se usan miembros `protected` o métodos plantilla, la subclase pasa a depender del funcionamiento interno y del orden de ejecución de la clase base.

Esa dependencia interna debilita la encapsulación, porque cambios "internos" de la superclase pueden afectar a subclases aunque el contrato público aparente no haya cambiado. En otras palabras, la implementación deja de ser completamente intercambiable y pasa a condicionar a quien hereda.

## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`

La alternativa por herencia modela el caso como una relación "es-un": `Estudiante` y `Trabajador` se consideran tipos de `Persona`. Es directa y legible cuando esa relación semántica es fuerte y estable. Los datos comunes se centralizan en la superclase, y las subclases añaden sus campos propios.

La alternativa por composición modela "tiene-un": tanto `Estudiante` como `Trabajador` contienen un objeto `DatosPersonales`. Esta forma suele dar mayor flexibilidad porque separa datos comunes de roles o comportamientos. Además, obliga a inyectar explícitamente `DatosPersonales` en el constructor, lo que hace más visible la dependencia.

```java
// Alternativa 1: Herencia
class Persona {
 private String dni;
 private String nombre;

 public Persona(String dni, String nombre) {
  this.dni = dni;
  this.nombre = nombre;
 }

 public String getDni() { return dni; }
 public String getNombre() { return nombre; }
}

class EstudianteH extends Persona {
 private String carrera;

 public EstudianteH(String dni, String nombre, String carrera) {
  super(dni, nombre);
  this.carrera = carrera;
 }
}

class TrabajadorH extends Persona {
 private String empresa;

 public TrabajadorH(String dni, String nombre, String empresa) {
  super(dni, nombre);
  this.empresa = empresa;
 }
}

// Alternativa 2: Composición
class DatosPersonales {
 private String dni;
 private String nombre;

 public DatosPersonales(String dni, String nombre) {
  this.dni = dni;
  this.nombre = nombre;
 }

 public String getDni() { return dni; }
 public String getNombre() { return nombre; }
}

class EstudianteC {
 private DatosPersonales datos;
 private String carrera;

 public EstudianteC(DatosPersonales datos, String carrera) {
  this.datos = datos;
  this.carrera = carrera;
 }
}

class TrabajadorC {
 private DatosPersonales datos;
 private String empresa;

 public TrabajadorC(DatosPersonales datos, String empresa) {
  this.datos = datos;
  this.empresa = empresa;
 }
}
```
