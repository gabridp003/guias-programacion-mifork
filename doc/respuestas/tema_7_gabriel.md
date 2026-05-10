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

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a una función es una variable que almacena la dirección de memoria de una función, permitiendo invocarla de forma indirecta. En C, las funciones no son objetos como en Java, pero sí pueden tratarse de forma similar a datos mediante este mecanismo. Esto permite, por ejemplo, seleccionar dinámicamente qué función ejecutar o pasar funciones como parámetros, lo que aporta flexibilidad al diseño del programa.

Para utilizar un puntero a función, es necesario declarar una variable cuyo tipo coincida exactamente con la firma de la función (tipo de retorno y parámetros). Posteriormente, se le asigna la función deseada y se invoca a través del puntero como si fuera una llamada normal. Este proceso resulta útil en patrones como callbacks o tablas de funciones, muy habituales en programación en C.

A continuación se muestra un ejemplo en C donde se define una función que recibe una cadena de caracteres y la convierte a mayúsculas. Se crea un puntero a función llamado aMayusculas, se le asigna la función y se invoca a través de él:

```java
#include <stdio.h>
#include <ctype.h>

void convertirAMayusculas(char *cadena) {
    for (int i = 0; cadena[i] != '\0'; i++) {
        cadena[i] = toupper(cadena[i]);
    }
}

int main() {
    char texto[] = "hola mundo";

    // Declarar puntero a función
    void (*aMayusculas)(char *);

    // Asignar la función al puntero
    aMayusculas = convertirAMayusculas;

    // Invocar la función mediante el puntero
    aMayusculas(texto);

    printf("%s\n", texto);

    return 0;
}
```

En este ejemplo, el puntero aMayusculas apunta a la función convertirAMayusculas y se utiliza para modificar la cadena original. De esta forma, se demuestra cómo las funciones pueden manipularse de forma indirecta en C, proporcionando un comportamiento más dinámico sin necesidad de orientación a objetos.


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima (sin nombre) que puede definirse directamente en el lugar donde se necesita. Este tipo de funciones es característico de lenguajes con soporte para programación funcional, y permite tratar las funciones como valores, es decir, asignarlas a variables, pasarlas como parámetros o devolverlas como resultado. En comparación con los punteros a funciones en C, las funciones lambda ofrecen una sintaxis más sencilla y expresiva, especialmente en lenguajes modernos como JavaScript y Java.

Las funciones lambda suelen utilizarse en situaciones donde se necesita una función pequeña y puntual, sin necesidad de declararla de forma completa. En JavaScript, las funciones son objetos de primera clase, por lo que se pueden asignar directamente a variables. En Java, a partir de Java 8, se introducen las expresiones lambda junto con interfaces funcionales como Function<T, R>, que permiten representar funciones de forma tipada.

A continuación se muestra un ejemplo en JavaScript equivalente al ejercicio anterior, utilizando una función lambda almacenada en la variable aMayusculas:

```java
const aMayusculas = (cadena) => {
    return cadena.toUpperCase();
};

let texto = "hola mundo";
console.log(aMayusculas(texto));
```

En Java, se puede lograr un comportamiento similar utilizando una expresión lambda y la interfaz funcional Function<String, String>:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();

        String texto = "hola mundo";
        System.out.println(aMayusculas.apply(texto));
    }
}
```

En ambos ejemplos, la función lambda se asigna a la variable aMayusculas, que posteriormente se invoca para transformar la cadena a mayúsculas. Este enfoque permite escribir código más conciso y flexible, facilitando la reutilización y la composición de funciones.


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación en el que el programa se construye principalmente mediante el uso de funciones puras, evitando en la medida de lo posible el estado mutable y los efectos secundarios. En este paradigma, se busca que las funciones siempre produzcan el mismo resultado para los mismos parámetros, lo que facilita el razonamiento sobre el código y mejora su mantenibilidad. A diferencia de la programación imperativa clásica en C, donde se modifican variables paso a paso, la programación funcional se centra en describir qué se quiere calcular más que cómo hacerlo.

Lenguajes como Java, a partir de la versión 8, se consideran multi-paradigma porque permiten combinar diferentes estilos de programación dentro del mismo lenguaje. Aunque Java es tradicionalmente orientado a objetos, incorpora características propias del paradigma funcional, como las funciones lambda, las interfaces funcionales y operaciones sobre colecciones (streams). Esto permite aprovechar ventajas de ambos enfoques: la organización estructurada de la orientación a objetos y la expresividad y concisión del enfoque funcional.

Decir que las funciones son “ciudadanos de primera clase” significa que las funciones pueden tratarse igual que cualquier otro valor del lenguaje. Es decir, pueden almacenarse en variables, pasarse como argumentos a otras funciones y devolverse como resultado. Este concepto es clave en la programación funcional, ya que permite construir abstracciones más flexibles y reutilizables. En lenguajes como JavaScript esto se cumple de forma natural, mientras que en Java se consigue mediante interfaces funcionales y expresiones lambda, aproximando este comportamiento.


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

En Java, una función lambda es una forma abreviada de definir una función anónima, es decir, una función sin nombre que puede pasarse como argumento o almacenarse en una variable. Su sintaxis básica sigue la estructura (parámetros) -> expresión o (parámetros) -> { bloque de código }. Esta sintaxis permite escribir funciones de forma más concisa que con clases anónimas tradicionales, facilitando la lectura y el mantenimiento del código.

Los parámetros de la lambda se colocan entre paréntesis, pudiendo omitirse cuando hay un único parámetro y el tipo se puede inferir automáticamente. A la derecha de la flecha -> se define el cuerpo de la función. Si el cuerpo contiene una única expresión, no es necesario usar llaves {} ni la palabra clave return, ya que se asume implícitamente. En cambio, si contiene varias instrucciones, entonces sí deben utilizarse llaves y, en caso de devolver un valor, incluir return.

Es importante tener en cuenta que las funciones lambda en Java siempre están asociadas a una interfaz funcional, es decir, una interfaz que contiene un único método abstracto (como Function<T, R>). Esto se debe a que Java no trata las funciones directamente como en lenguajes puramente funcionales, sino que utiliza estas interfaces para simular ese comportamiento. Por tanto, la lambda actúa como implementación de ese único método abstracto.

Un ejemplo sencillo de sintaxis de una función lambda en Java sería el siguiente:

```java
import java.util.function.Function;

Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();
```

En este caso, (cadena) es el parámetro de entrada, -> separa los parámetros del cuerpo, y cadena.toUpperCase() es la expresión que se ejecuta y se devuelve. Esta sintaxis permite definir funciones de forma clara y compacta, integrándose con las características funcionales introducidas en Java 8.


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Para trabajar con funciones como parámetros, tanto en JavaScript como en Java es posible definir un método que reciba una función transformadora y la aplique a un dato. Este enfoque es una aplicación directa del paradigma funcional, ya que permite desacoplar el comportamiento (la transformación) del método que lo utiliza. De esta forma, el método no necesita conocer los detalles de la operación, sino simplemente ejecutar la función que recibe.

En JavaScript, esto resulta natural porque las funciones son ciudadanos de primera clase. Se puede definir un método transformar que reciba una cadena y una función, y que invoque dicha función sobre el texto. La función lambda aMayusculas se pasa directamente como argumento:

```java
const aMayusculas = (cadena) => {
    return cadena.toUpperCase();
};

function transformar(texto, funcion) {
    return funcion(texto);
}

let resultado = transformar("hola mundo", aMayusculas);
console.log(resultado);
```

En este ejemplo, transformar recibe la cadena y la función aMayusculas, y la ejecuta internamente. Esto permite reutilizar transformar con cualquier otra función que tenga la misma firma.

En Java se puede implementar el mismo patrón utilizando interfaces funcionales, como Function<String, String>. En este caso, el método transformar recibe una cadena y una función, y se invoca utilizando el método apply:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();

        String resultado = transformar("hola mundo", aMayusculas);
        System.out.println(resultado);
    }

    public static String transformar(String texto, Function<String, String> funcion) {
        return funcion.apply(texto);
    }
}
``` 

En este caso, el método transformar es independiente de la operación concreta que se realice sobre la cadena. Esto permite reutilizarlo con diferentes funciones lambda, lo que mejora la modularidad y facilita la extensión del programa sin modificar el código existente.


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Es posible invocar una función lambda directamente en la llamada a un método sin necesidad de almacenarla previamente en una variable. Este enfoque resulta muy común en programación funcional, ya que permite definir comportamientos de forma puntual y localizada, evitando la creación de variables innecesarias cuando la función solo se va a utilizar una vez.

En JavaScript, dado que las funciones son ciudadanos de primera clase, se puede pasar una función lambda directamente como argumento en la llamada a transformar. En este caso, se define una función que invierte la cadena en el mismo momento en el que se realiza la llamada:

```java
function transformar(texto, funcion) {
    return funcion(texto);
}

let resultado = transformar("hola mundo", (cadena) => {
    return cadena.split("").reverse().join("");
});

console.log(resultado);
``` 

En este ejemplo, la función lambda se define inline y realiza la inversión de la cadena utilizando métodos propios de JavaScript. Esto permite escribir código más conciso y directamente asociado a la operación que se quiere realizar.

En Java, también es posible pasar una lambda directamente como parámetro al método transformar. Se utiliza nuevamente la interfaz funcional Function<String, String>, pero la lambda se define en la propia llamada:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        String resultado = transformar("hola mundo", 
            (cadena) -> new StringBuilder(cadena).reverse().toString()
        );

        System.out.println(resultado);
    }

    public static String transformar(String texto, Function<String, String> funcion) {
        return funcion.apply(texto);
    }
}
```
En este caso, la función lambda invierte la cadena utilizando la clase StringBuilder. Este estilo de programación permite definir funciones de manera puntual, mejorando la legibilidad cuando la operación es sencilla y no se requiere reutilización en otras partes del código.


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre o closure es una función que es capaz de acceder a variables que están definidas fuera de su propio ámbito, es decir, en el contexto donde fue creada. En programación funcional, esto permite que una función “recuerde” el entorno en el que se definió, incluso aunque se ejecute en otro lugar del programa. Este comportamiento es clave para construir funciones más flexibles y reutilizables.

En Java, las funciones lambda pueden capturar variables locales del contexto en el que se definen, pero con una restricción importante: esas variables deben ser finales o efectivamente finales (es decir, no deben modificarse después de su inicialización). Esto garantiza un comportamiento seguro y predecible. Gracias a esta característica, las lambdas pueden utilizar valores externos sin necesidad de pasarlos explícitamente como parámetros.

A continuación se muestra una modificación del ejemplo anterior, donde se define una variable local externa a la lambda, y se utiliza dentro de la función para concatenar texto:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {

        String sufijo = "!!!"; // Variable local capturada (closure)

        String resultado = transformar("hola mundo",
            (cadena) -> cadena + sufijo
        );

        System.out.println(resultado);
    }

    public static String transformar(String texto, Function<String, String> funcion) {
        return funcion.apply(texto);
    }
}
```

En este caso, la lambda (cadena) -> cadena + sufijo utiliza la variable sufijo, que está definida fuera de su ámbito directo. Esto demuestra el concepto de cierre, ya que la función “captura” esa variable y la usa al ejecutarse. Este mecanismo permite escribir código más expresivo y evitar pasar múltiples parámetros cuando las funciones necesitan acceso a un contexto previo.



## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Una función lambda y un puntero a función en C permiten invocar funciones de forma indirecta, pero presentan diferencias importantes en su concepto y uso. En C, un puntero a función simplemente almacena la dirección de una función ya definida, por lo que no crea nuevas funciones ni permite definir comportamiento en el momento. En cambio, una función lambda permite definir una función directamente donde se necesita, sin necesidad de declararla previamente, lo que aporta mayor flexibilidad y expresividad.

Otra diferencia clave es la capacidad de capturar contexto. Los punteros a funciones en C no pueden acceder automáticamente a variables locales del entorno donde se utilizan; únicamente trabajan con los parámetros que se les pasan. En cambio, las funciones lambda pueden actuar como closures, capturando variables del contexto externo (con ciertas restricciones en Java). Esto permite que una lambda tenga acceso a información adicional sin necesidad de incluirla explícitamente como parámetro.

Además, en lenguajes como Java o JavaScript, las funciones lambda forman parte de un modelo donde las funciones son ciudadanos de primera clase (o se aproximan a ello, en el caso de Java). Esto implica que pueden crearse dinámicamente, almacenarse, pasarse y combinarse fácilmente. En C, aunque los punteros a funciones permiten cierto grado de abstracción, su uso es más limitado y menos intuitivo, ya que no existe un soporte directo para definir funciones anónimas ni para trabajar con ellas de forma tan flexible.

En resumen, mientras que los punteros a funciones en C son un mecanismo básico para referenciar funciones existentes, las funciones lambda forman parte de un enfoque más moderno y potente asociado al paradigma funcional. Estas permiten escribir código más conciso, reutilizable y adaptable, especialmente cuando se combinan con otras características como interfaces funcionales o el paso de funciones como parámetros.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

En este caso se plantea una función que devuelve otra función, lo cual es una característica típica del paradigma funcional. La idea es que crearDescuento reciba un porcentaje y construya dinámicamente una función que aplique ese descuento a una cantidad. Para representar estas funciones se utiliza Function<Double, Double>, donde el parámetro y el resultado son valores numéricos de tipo Double.

La clave del ejercicio está en que la función devuelta “recuerda” el porcentaje de descuento que se le pasó al crearla. Es decir, aunque la función se utilice más adelante, mantiene acceso al valor original. Esto es precisamente un ejemplo de closure (cierre), ya que la función lambda captura una variable del entorno en el que fue definida.

A continuación se muestra una posible implementación en Java:

```java
import java.util.function.Function;

public class Main {

    public static void main(String[] args) {

        // Crear dos funciones de descuento distintas
        Function<Double, Double> descuento10 = crearDescuento(10.0);
        Function<Double, Double> descuento20 = crearDescuento(20.0);

        double precio = 100.0;

        System.out.println("Precio original: " + precio);
        System.out.println("Con 10% descuento: " + descuento10.apply(precio));
        System.out.println("Con 20% descuento: " + descuento20.apply(precio));
    }

    public static Function<Double, Double> crearDescuento(Double porcentaje) {
        return (cantidad) -> cantidad - (cantidad * porcentaje / 100);
    }
}
```

En este ejemplo, cada llamada a crearDescuento genera una función distinta. La lambda (cantidad) -> ... utiliza la variable porcentaje, que está definida fuera de su ámbito directo, lo que constituye el closure. Gracias a esto, descuento10 y descuento20 se comportan de manera diferente, aunque se hayan creado a partir de la misma función. Este mecanismo permite construir funciones personalizadas de forma flexible y reutilizable.


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional en Java es una interfaz que define un único método abstracto, y que se utiliza como tipo para representar funciones, especialmente expresiones lambda. Este concepto es fundamental para integrar la programación funcional en un lenguaje con tipado estático como Java, ya que permite asignar un tipo concreto a una lambda. Por ejemplo, la interfaz Function<T, R> representa funciones que reciben un valor de tipo T y devuelven un valor de tipo R.

El principal requisito de una interfaz funcional es que tenga exactamente un método abstracto. Puede incluir otros métodos, pero deben ser métodos default (con implementación) o static, que no cuentan como métodos abstractos. Además, aunque no es obligatorio, se puede usar la anotación @FunctionalInterface para indicar explícitamente que la interfaz está diseñada para este propósito y para que el compilador verifique que se cumple la condición.

Otro aspecto importante es que una función lambda en Java siempre se asocia a una interfaz funcional concreta. Es decir, no existe una “función pura” como en JavaScript, sino que la lambda actúa como implementación del único método abstracto de dicha interfaz. Esto permite mantener el control de tipos y garantizar la coherencia en tiempo de compilación.

Por ejemplo, en el caso anterior:

```java
Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();
```

la lambda implementa el método apply de la interfaz Function. Gracias a las interfaces funcionales, Java consigue combinar la seguridad del tipado estático con la flexibilidad de la programación funcional


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Una interfaz funcional definida a mano es simplemente una interfaz en Java que cumple la condición de tener un único método abstracto, lo que permite utilizarla como tipo para expresiones lambda. En este caso, se puede crear una interfaz llamada Transformador cuyo propósito sea recibir una cadena (String) y devolver otra cadena transformada. Esta interfaz será equivalente a usar Function<String, String>, pero definida de forma personalizada.

Para ello, basta con declarar una interfaz con un único método, por ejemplo llamado transformar. Además, es recomendable usar la anotación @FunctionalInterface, ya que permite al compilador comprobar que la interfaz cumple correctamente la condición de ser funcional (es decir, que no tiene más de un método abstracto).

A continuación se muestra la definición de la interfaz:

```java
@FunctionalInterface
public interface Transformador {
    String transformar(String texto);
}
```

Una vez definida esta interfaz, se puede utilizar como tipo de referencia para almacenar funciones lambda. Por ejemplo:

```java
public class Main {
    public static void main(String[] args) {

        Transformador aMayusculas = (cadena) -> cadena.toUpperCase();

        String resultado = aMayusculas.transformar("hola mundo");
        System.out.println(resultado);
    }
}
```

En este ejemplo, la lambda implementa el método transformar de la interfaz Transformador. De esta forma, se observa cómo es posible crear interfaces funcionales propias para representar funciones con un significado más específico, mejorando la claridad y reutilización del código.


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Se puede generalizar la interfaz funcional Transformador utilizando generics para que no esté limitada únicamente a String, sino que permita transformar cualquier tipo en otro. Esto se consigue definiendo parámetros de tipo genéricos, por ejemplo <T, R>, donde T representa el tipo de entrada y R el tipo de salida. De esta forma, la interfaz se vuelve reutilizable para distintos tipos de datos, manteniendo la seguridad de tipos propia de Java.

La interfaz genérica se puede definir de la siguiente manera, manteniendo un único método abstracto para cumplir con los requisitos de interfaz funcional:

```java
@FunctionalInterface
public interface Transformador<T, R> {
    R transformar(T valor);
}
```

En este caso, T es el tipo del parámetro de entrada y R es el tipo del resultado. Esta interfaz es conceptualmente equivalente a Function<T, R>, pero con un nombre más específico definido por el usuario.

A continuación se muestra un ejemplo de uso donde se crea un transformador que convierte un Double en un Integer mediante redondeo:

```java
public class Main {
    public static void main(String[] args) {

        Transformador<Double, Integer> redondear = 
            (valor) -> (int) Math.round(valor);

        Double numero = 3.7;
        Integer resultado = redondear.transformar(numero);

        System.out.println(resultado); // imprime 4
    }
}
```

En este ejemplo, la lambda toma un valor de tipo Double y devuelve un Integer redondeado. Gracias al uso de generics, la interfaz Transformador permite definir funciones de transformación sobre cualquier tipo, mejorando la reutilización y evitando tener que crear una interfaz distinta para cada caso concreto.


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java proporciona un conjunto de interfaces funcionales predefinidas en el paquete java.util.function, con el objetivo de cubrir los casos más comunes sin necesidad de definir nuevas interfaces. Estas interfaces permiten trabajar con funciones lambda de forma estándar, reutilizable y tipada, siguiendo el paradigma funcional dentro del lenguaje. Entre ellas destaca especialmente Function<T, R>, que es equivalente al Transformador<T, R> definido previamente.
Las principales interfaces funcionales predefinidas son:

Function<T, R> → recibe un valor de tipo T y devuelve uno de tipo R
Predicate<T> → recibe un valor de tipo T y devuelve un boolean (condición)
Consumer<T> → recibe un valor de tipo T y no devuelve nada (void)
Supplier<T> → no recibe parámetros y devuelve un valor de tipo T

También existen variantes más específicas, como:

UnaryOperator<T> → caso particular de Function donde entrada y salida son del mismo tipo
BinaryOperator<T> → recibe dos parámetros del mismo tipo y devuelve un resultado del mismo tipo
Versiones para tipos primitivos (IntFunction, DoubleConsumer, etc.) para evitar autoboxing

Por ejemplo, el caso ya visto se puede expresar con una interfaz estándar:

```java
import java.util.function.Function;

Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();
```

Gracias a estas interfaces predefinidas, no es necesario crear interfaces funcionales propias en muchos casos, lo que simplifica el desarrollo y mejora la interoperabilidad del código. Sin embargo, definir interfaces propias como Transformador puede seguir siendo útil cuando se quiere dar un significado más claro o específico al comportamiento representado.


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

El método forEach en Java es una forma funcional de recorrer colecciones, como listas, introducida a partir de Java 8 como parte de la API de streams y operaciones funcionales. A diferencia del bucle for tradicional, forEach recibe una función (normalmente una lambda) que se aplica a cada elemento de la colección. Este enfoque permite escribir código más declarativo, centrándose en la acción que se quiere realizar sobre cada elemento, en lugar de en el control del índice o la iteración manual.

El método forEach pertenece a la interfaz Iterable y acepta como parámetro un Consumer<T>, es decir, una función que recibe un elemento y no devuelve nada. Esto encaja perfectamente con el paradigma funcional, donde es habitual pasar funciones como parámetros. En lugar de escribir un bucle explícito, se delega el recorrido de la colección en el propio método, haciendo el código más limpio y expresivo.

A continuación se muestra un ejemplo donde se recorre una lista de enteros y se muestra un mensaje si el valor es positivo:

```java
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {

        List<Integer> numeros = Arrays.asList(-3, 5, 0, 8, -2);

        numeros.forEach((n) -> {
            if (n > 0) {
                System.out.println("Número positivo: " + n);
            }
        });
    }
}
```

En este ejemplo, la lambda (n) -> { ... } se ejecuta para cada elemento de la lista. Este estilo evita el uso de índices y estructuras de control explícitas como en los bucles for, y permite expresar la lógica de forma más clara y directa. Aunque internamente el comportamiento es equivalente a un bucle, el uso de forEach encaja mejor con el estilo de programación funcional introducido en Java moderno.

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

El método forEach está definido como forEach(Consumer<? super T> action) en lugar de Consumer<T> para permitir mayor flexibilidad en el uso de generics. En concreto, ? super T indica que el Consumer puede trabajar no solo con objetos de tipo T, sino también con cualquiera de sus supertipos. Esto permite reutilizar funciones más generales en contextos donde se trabaja con tipos más específicos, evitando restricciones innecesarias en el sistema de tipos.
Este uso se basa en el principio conocido como PECS (Producer Extends, Consumer Super). Esta regla establece que:

Si una estructura produce datos (solo se leen), se debe usar ? extends T
Si una estructura consume datos (solo se escriben), se debe usar ? super T

En el caso de Consumer, este recibe valores (los “consume”), por lo que tiene sentido usar ? super T. De esta forma, si se tiene una lista de Integer, se puede usar un Consumer<Number> o incluso Consumer<Object>, ya que ambos pueden aceptar Integer.

Este mismo principio puede aplicarse para mejorar la definición del método transformar. En lugar de escribirlo como:

```java
public static String transformar(String texto, Function<String, String> funcion) {
    return funcion.apply(texto);
}
```

se puede generalizar y hacer más flexible utilizando generics y PECS:

```java
public static <T, R> R transformar(T valor, Function<? super T, ? extends R> funcion) {
    return funcion.apply(valor);
}
```

En esta versión:

super T permite que la función acepte tipos más generales que T (consumo de datos)
extends R permite devolver tipos más específicos que R (producción de datos)

De esta forma, el método transformar se vuelve más reutilizable y coherente con el sistema de tipos de Java. La aplicación de PECS permite diseñar APIs más flexibles y seguras, evitando limitaciones innecesarias al trabajar con genéricos.

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Las referencias a métodos permiten utilizar métodos ya existentes como si fueran funciones, sin necesidad de escribir una lambda explícita. Este mecanismo es una forma más concisa y legible de expresar ciertas operaciones, especialmente cuando la lambda simplemente llama a un método ya definido. Tanto en JavaScript como en Java, es posible almacenar una referencia a un método en una variable y posteriormente invocarlo.

En JavaScript, los métodos de un objeto pueden asignarse directamente a variables, ya que las funciones son ciudadanos de primera clase. Sin embargo, es importante tener en cuenta el contexto (this), por lo que a menudo se utiliza bind para mantener la referencia al objeto original. A continuación se muestra un ejemplo con una clase Persona:

```java

class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }

    saludar() {
        return "Hola, soy " + this.nombre;
    }
}

const p = new Persona("Gabriel");

// Obtener referencia al método
const refSaludar = p.saludar.bind(p);

// Invocar usando la referencia
console.log(refSaludar());
```

En este caso, refSaludar contiene una referencia al método saludar ligada al objeto p, lo que permite invocarlo correctamente.

En Java, las referencias a métodos se introducen en Java 8 y se expresan mediante el operador ::. Permiten referenciar métodos de instancia, métodos estáticos o constructores. En este caso, se obtiene una referencia al método saludar de un objeto concreto:

```java
import java.util.function.Supplier;

public class Main {

    public static void main(String[] args) {

        Persona p = new Persona("Gabriel");

        // Referencia al método de instancia
        Supplier<String> refSaludar = p::saludar;

        // Invocar usando la referencia
        System.out.println(refSaludar.get());
    }
}

class Persona {
    private String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }

    public String saludar() {
        return "Hola, soy " + nombre;
    }
}
```

En este ejemplo, p::saludar crea una referencia al método saludar del objeto p, y se asigna a un Supplier<String>, ya que no recibe parámetros y devuelve un String. Este tipo de referencias mejora la legibilidad del código frente a una lambda equivalente como () -> p.saludar(), manteniendo el mismo comportamiento.


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

En Java existen varios tipos de referencias a método, que permiten reutilizar métodos ya definidos de forma más concisa que usando expresiones lambda. Estas referencias se expresan con el operador :: y siempre se asocian a una interfaz funcional compatible. Los principales tipos de referencias son: a métodos estáticos, a constructores, a métodos de instancia de un objeto concreto y a métodos de instancia de cualquier objeto de una clase.

A continuación se muestran ejemplos de cada tipo:

1. Referencia a método estático
Se utiliza cuando el método pertenece a la clase y no a una instancia concreta:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {

        Function<String, Integer> refLongitud = String::length;

        System.out.println(refLongitud.apply("hola"));
    }
}
```

En este caso, String::length hace referencia a un método que se aplicará sobre el objeto que se pase como parámetro.

2. Referencia a constructor
Se utiliza para crear objetos de una clase:

```java
import java.util.function.Function;

class Persona {
    String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }
}

public class Main {
    public static void main(String[] args) {

        Function<String, Persona> crearPersona = Persona::new;

        Persona p = crearPersona.apply("Gabriel");
        System.out.println(p.nombre);
    }
}
```

Aquí, Persona::new referencia al constructor que recibe un String.

3. Referencia a método de instancia de un objeto concreto
Se refiere a un método ligado a un objeto específico:

```java
import java.util.function.Supplier;

class Persona {
    String nombre;

    public Persona(String nombre) {
        this.nombre = nombre;
    }

    public String saludar() {
        return "Hola, soy " + nombre;
    }
}

public class Main {
    public static void main(String[] args) {

        Persona p = new Persona("Gabriel");

        Supplier<String> refSaludar = p::saludar;

        System.out.println(refSaludar.get());
    }
}
```

En este caso, p::saludar está vinculado a la instancia concreta p.

4. Referencia a método de instancia sobre cualquier instancia
Se utiliza cuando el método se aplicará a distintos objetos, y se pasa el objeto como parámetro:

```java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {

        Function<String, String> refMayusculas = String::toUpperCase;

        System.out.println(refMayusculas.apply("hola"));
    }
}
```

Aquí, String::toUpperCase indica que el método se aplicará al objeto que se reciba como argumento.

En resumen, las referencias a método permiten reutilizar código existente de forma más directa y legible, sustituyendo muchas lambdas simples. Esto mejora la claridad del código y refuerza el estilo funcional introducido en Java moderno.


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

Para ordenar una lista de objetos en Java, se puede utilizar el método Collections.sort, que recibe como segundo parámetro un Comparator. Este comparador define cómo se deben ordenar los elementos, y puede implementarse mediante una expresión lambda. En este caso, se quiere ordenar una lista de Persona primero por edad, y en caso de empate, por nombre en orden alfabético.

En la primera versión, se puede implementar la comparación manualmente dentro de la lambda, comparando directamente los atributos de los objetos:

```java
import java.util.*;

class Persona {
    String nombre;
    int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }
}

public class Main {
    public static void main(String[] args) {

        List<Persona> personas = Arrays.asList(
            new Persona("Ana", 25),
            new Persona("Luis", 30),
            new Persona("Carlos", 25),
            new Persona("Bea", 30)
        );

        Collections.sort(personas, (p1, p2) -> {
            if (p1.edad < p2.edad) return -1;
            if (p1.edad > p2.edad) return 1;
            return p1.nombre.compareTo(p2.nombre);
        });

        personas.forEach(p -> 
            System.out.println(p.nombre + " - " + p.edad)
        );
    }
}
```

En este caso, la lambda compara primero la edad manualmente, y si ambas edades son iguales, utiliza compareTo sobre los nombres.

En la segunda versión, se puede utilizar la clase Comparator, que proporciona métodos auxiliares más expresivos y seguros, como comparing y thenComparing. Esto permite escribir el mismo comportamiento de forma más declarativa:

```java
import java.util.*;

class Persona {
    String nombre;
    int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }
}

public class Main {
    public static void main(String[] args) {

        List<Persona> personas = Arrays.asList(
            new Persona("Ana", 25),
            new Persona("Luis", 30),
            new Persona("Carlos", 25),
            new Persona("Bea", 30)
        );

        Collections.sort(personas, 
            Comparator.comparing((Persona p) -> p.edad)
                      .thenComparing(p -> p.nombre)
        );

        personas.forEach(p -> 
            System.out.println(p.nombre + " - " + p.edad)
        );
    }
}
```

En esta segunda versión, Comparator.comparing define el criterio principal (edad), y thenComparing añade el criterio secundario (nombre). Este enfoque es más limpio, más fácil de leer y menos propenso a errores que la comparación manual, especialmente cuando se encadenan múltiples criterios de ordenación.
