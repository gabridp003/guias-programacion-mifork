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

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

Es posible construir estructuras de datos capaces de almacenar cualquier tipo de dato utilizando mecanismos de genericidad básicos que ofrecen los lenguajes. En C, al no existir tipos genéricos propiamente dichos, esta flexibilidad se consigue mediante el uso de punteros a void (void*), que pueden referenciar direcciones de memoria de cualquier tipo. De esta forma, una misma estructura puede almacenar enteros, flotantes, estructuras u otros tipos, aunque esta técnica renuncia al chequeo de tipos en tiempo de compilación.

Un ejemplo típico consiste en definir una estructura que contenga un array primitivo de void*, junto con información adicional como el tamaño y la capacidad. Cada posición del array puede apuntar a un dato de cualquier tipo, siendo responsabilidad del programador saber qué tipo se almacena en cada caso y realizar las conversiones oportunas al recuperarlo. Este enfoque aporta gran flexibilidad, pero también introduce riesgos, como errores de conversión o accesos inválidos a memoria.

En Java, una solución equivalente anterior a los genéricos consiste en utilizar la clase base Object, ya que todas las clases heredan de ella. Un array de Object permite almacenar instancias de cualquier clase, cumpliendo un papel similar al de void* en C. Sin embargo, al igual que ocurre en C, este enfoque pierde seguridad de tipos, ya que al recuperar los elementos es necesario realizar conversiones explícitas (casting), lo que puede provocar errores en tiempo de ejecución.

Estos ejemplos representan formas básicas y no seguras de genericidad, anteriores a los mecanismos modernos como los genéricos de Java. Su utilidad principal es ilustrar cómo se puede lograr generalidad usando únicamente herramientas elementales del lenguaje, poniendo de manifiesto tanto sus posibilidades como sus limitaciones.

```java
// Ejemplo en Java usando Object
public class ListaGenerica {
    private Object[] datos;

    public ListaGenerica(int capacidad) {
        datos = new Object[capacidad];
    }

    public void set(int index, Object elemento) {
        datos[index] = elemento;
    }

    public Object get(int index) {
        return datos[index];
    }
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica consiste en definir estructuras de datos y algoritmos de forma que puedan trabajar con distintos tipos de datos sin cambiar su implementación. Para ello, el tipo de los datos no se fija de antemano, sino que se deja abstracto, permitiendo reutilizar el mismo código para diferentes tipos y evitando duplicaciones innecesarias. El objetivo principal es aumentar la reutilización y mejorar la claridad del diseño.

Un aspecto fundamental de la programación genérica es el chequeo de tipos, preferiblemente en tiempo de compilación. En los lenguajes que soportan genericidad de forma nativa, se busca que el compilador pueda detectar usos incorrectos de los tipos, evitando errores en tiempo de ejecución. Esto diferencia la programación genérica real de otras aproximaciones más simples que solo proporcionan flexibilidad.

El ejemplo anterior basado en void* en C o Object en Java no es programación genérica en sentido estricto. Aunque permite almacenar cualquier tipo de dato, la información de tipo se pierde y debe recuperarse manualmente mediante conversiones explícitas, lo que elimina la seguridad de tipos. Por tanto, el ejemplo puede considerarse un ejemplo básico de generalidad, pero no un ejemplo completo de programación genérica.

En conclusión, el ejemplo anterior ilustra la idea de trabajar con datos de distintos tipos, pero carece de los mecanismos de seguridad y abstracción que caracterizan a la programación genérica propiamente dicha.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El principal problema en cuanto al chequeo de tipos al emplear void* en C o Object en Java es que se pierde la información de tipo en tiempo de compilación. Al almacenar datos mediante estos mecanismos, el compilador deja de tener conocimiento sobre el tipo real de los elementos contenidos en la estructura de datos, por lo que no puede comprobar si las operaciones que se realizan sobre ellos son correctas. Esto elimina una de las principales ventajas del tipado estático: la detección temprana de errores.

Como consecuencia directa, el programador se ve obligado a realizar conversiones explícitas de tipo (casting) al recuperar los datos. Estas conversiones no pueden verificarse completamente en compilación y, si se realizan de forma incorrecta, provocan errores en tiempo de ejecución, como accesos inválidos a memoria en C o excepciones de tipo ClassCastException en Java. El error, por tanto, no se detecta cuando se compila el programa, sino cuando ya se está ejecutando.

Además, este enfoque hace que el código sea más frágil y difícil de mantener, ya que el correcto funcionamiento depende de que el programador recuerde qué tipo concreto se ha almacenado en cada posición de la estructura. No existe ninguna garantía automática que impida insertar un tipo distinto del esperado o que ayude a documentar ese uso de forma clara en el código.

En resumen, el uso de void* u Object permite construir estructuras de datos flexibles, pero a costa de renunciar a la seguridad de tipos, trasladando la responsabilidad del control de tipos del compilador al programador. Este inconveniente es una de las principales motivaciones para la introducción de mecanismos de programación genérica más avanzados, que mantienen la generalidad sin perder el chequeo de tipos en compilación.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son mecanismos que permiten definir clases, métodos o interfaces sin fijar el tipo concreto de los datos con los que van a trabajar. En lugar de usar un tipo específico, se introduce un identificador simbólico (como T), que representa un tipo que se determinará más adelante, cuando la clase o el método se utilice. De este modo, el mismo código puede adaptarse a distintos tipos sin necesidad de duplicar la implementación.

La principal ventaja de los parámetros de tipo es que mantienen la información de los tipos en tiempo de compilación, a diferencia de soluciones basadas en void* u Object. El compilador puede comprobar que los usos del tipo son correctos, detectar errores antes de la ejecución y evitar conversiones explícitas inseguras. Esto mejora notablemente la seguridad y la robustez del código genérico.

Además, los parámetros de tipo permiten expresar de forma clara qué tipo de datos espera y devuelve una estructura o un algoritmo, haciendo el código más legible y autoexplicativo. El programador que utiliza una clase genérica sabe exactamente con qué tipo trabaja, y el contrato queda reflejado directamente en la definición de la clase o del método.

En resumen, los parámetros de tipo constituyen la base de la programación genérica moderna, ya que combinan reutilización de código y chequeo de tipos en compilación, resolviendo los principales problemas asociados a las aproximaciones genéricas más primitivas.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java, la programación genérica se implementa mediante generics, que permiten parametrizar clases y estructuras de datos con un tipo concreto. Al instanciar una estructura genérica indicando el tipo String, el compilador garantiza que solo se podrán insertar objetos de ese tipo, y que al recuperarlos no será necesario realizar conversiones explícitas. Esto proporciona seguridad de tipos en tiempo de compilación y evita errores habituales asociados al uso de Object.

Al utilizar una lista genérica de String, es posible añadir elementos y recorrerlos sabiendo que cada elemento recuperado es directamente de tipo String. El compilador impide insertar objetos de otros tipos y asegura que los métodos específicos de String pueden utilizarse con total seguridad durante el recorrido.

En C++, la genericidad se consigue mediante templates, que permiten definir clases y estructuras parametrizadas por tipos. Al instanciar un std::vector<std::string>, el compilador genera una versión concreta del contenedor especializada para std::string. Al igual que en Java, esto garantiza que solo se almacenan cadenas y que cada elemento recuperado tiene el tipo correcto, con chequeo completo en compilación.

En ambos casos, se observa cómo la programación genérica permite reutilizar estructuras estándar manteniendo seguridad de tipos, eliminando la necesidad de conversiones manuales y reduciendo errores en tiempo de ejecución.


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Cuando se instancia una clase con parámetros de tipo, el compilador debe adaptar esa definición genérica a un uso concreto. En términos generales, el compilador verifica que los tipos utilizados son correctos y que las operaciones realizadas sobre ellos respetan las restricciones impuestas por la definición genérica. Sin embargo, el modo en que se lleva a cabo este proceso depende del lenguaje, y Java y C++ siguen estrategias distintas.

En Java, el compilador aplica un mecanismo llamado type erasure (borrado de tipos). Esto significa que la información sobre los parámetros de tipo se utiliza solo en tiempo de compilación para realizar el chequeo de tipos, pero se elimina en tiempo de ejecución. Tras la compilación, todas las instancias genéricas se tratan como si trabajaran con Object (o con el límite superior que se haya definido). Por este motivo, no existen tipos genéricos distintos en tiempo de ejecución y no es posible, por ejemplo, comprobar el tipo genérico real con instanceof.

En C++, el enfoque es completamente diferente y se basa en la instanciación de plantillas (template instantiation). Cuando se utiliza una plantilla con un tipo concreto, el compilador genera una versión específica del código para ese tipo. Cada combinación de plantilla y tipo produce código distinto, con tipos completamente resueltos en compilación. Esto hace que el chequeo de tipos sea muy estricto y que no se pierda información de tipo en tiempo de ejecución.

En resumen, Java y C++ no hacen lo mismo al instanciar código genérico. Java prioriza la compatibilidad y simplicidad mediante el borrado de tipos, mientras que C++ apuesta por la generación de código específico para cada tipo. Ambas soluciones permiten programación genérica, pero con implicaciones distintas en rendimiento, seguridad de tipos y comportamiento en tiempo de ejecución.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

En Java, es posible definir clases con parámetros de tipo para crear estructuras reutilizables que puedan trabajar con distintos tipos de datos manteniendo la seguridad de tipos en tiempo de compilación. Una clase genérica puede declarar uno o varios parámetros de tipo, que actúan como marcadores del tipo concreto que se utilizará cuando se instancie la clase. Esto evita el uso de Object y elimina la necesidad de conversiones explícitas inseguras.

La clase Par puede definirse utilizando dos parámetros de tipo, lo que permite almacenar dos valores de tipos potencialmente distintos en una misma estructura. Cada parámetro de tipo representa el tipo concreto de uno de los valores, y se utiliza tanto en los atributos como en el constructor y los métodos de acceso (getters). De este modo, el compilador puede comprobar que los valores usados son coherentes con los tipos declarados.

Este tipo de clase resulta útil, por ejemplo, para devolver múltiples resultados de una función sin crear clases específicas para cada caso. Un uso típico sería devolver en un Par la media y la desviación típica de un conjunto de datos numéricos. Al parametrizar el Par con Double, se garantiza que ambos valores son del tipo correcto y que pueden utilizarse con total seguridad.

En consecuencia, los parámetros de tipo permiten crear estructuras flexibles, reutilizables y seguras, facilitando diseños más claros y evitando errores asociados a aproximaciones genéricas más primitivas.

✅ Definición de la clase genérica Par

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
```


✅ Ejemplo de uso: media y desviación típica

```java
public class Estadistica {

    public static Par<Double, Double> calcular(double[] datos) {
        double suma = 0.0;
        for (double d : datos) {
            suma += d;
        }
        double media = suma / datos.length;

        double sumaCuadrados = 0.0;
        for (double d : datos) {
            sumaCuadrados += (d - media) * (d - media);
        }
        double desviacion = Math.sqrt(sumaCuadrados / datos.length);

        return new Par<>(media, desviacion);
    }

    public static void main(String[] args) {
        double[] valores = {2.0, 4.0, 6.0};
        Par<Double, Double> resultado = calcular(valores);

        System.out.println("Media: " + resultado.getPrimero());
        System.out.println("Desviación típica: " + resultado.getSegundo());
    }
}
```

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

En Java, los parámetros de tipo no solo pueden declararse a nivel de clase, sino también a nivel de método. Un método genérico define sus propios parámetros de tipo independientes de los de la clase, lo que permite escribir funciones reutilizables y seguras incluso dentro de clases no genéricas. Este enfoque resulta especialmente útil para operaciones puntuales que deben funcionar sobre distintos tipos sin perder chequeo de tipos en compilación.

Si se define un método utilizando Object, este puede aceptar cualquier tipo de objeto, pero introduce dos problemas importantes. En primer lugar, obliga a realizar downcasting al recuperar el valor devuelto, con el riesgo de provocar errores en tiempo de ejecución. En segundo lugar, no fuerza que ambos parámetros sean del mismo tipo, permitiendo llamadas incoherentes que el compilador no puede detectar.

En cambio, al definir el método con parámetros de tipo, el compilador garantiza que ambos argumentos son del mismo tipo concreto y que el valor devuelto conserva ese tipo. Esto elimina la necesidad de conversiones explícitas y traslada el control de errores al tiempo de compilación, mejorando la seguridad y claridad del código.

Por tanto, los métodos genéricos permiten expresar de forma precisa las restricciones de tipo deseadas, evitando errores típicos asociados al uso de Object y proporcionando un diseño más robusto y mantenible.

❌ Versión con Object (sin genéricos)

```java
import java.util.Random;

public class Utilidades {

    public static Object seleccionaUno(Object a, Object b) {
        Random r = new Random();
        return r.nextBoolean() ? a : b;
    }
}
```
Problemas:

Es necesario hacer downcasting al resultado.
No se fuerza que a y b sean del mismo tipo.

✅ Versión con método genérico

```java
import java.util.Random;

public class Utilidades {

    public static <T> T seleccionaUno(T a, T b) {
        Random r = new Random();
        return r.nextBoolean() ? a : b;
    }
}
```

✅ Ejemplo de uso seguro

```java
String resultado = Utilidades.seleccionaUno("Hola", "Adios");
```

En este caso:

No hay downcasting (i).
El compilador impide pasar objetos de distinto tipo (ii), garantizando seguridad de tipos en compilación.


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

En Java sí se pueden establecer restricciones en los parámetros de tipo, lo que se conoce como parámetros de tipo acotados (bounded type parameters). Esto permite indicar que un parámetro genérico debe ser, como mínimo, de una determinada clase o interfaz. Por ejemplo, al definir un tipo genérico <T extends Number>, se garantiza que el tipo concreto será algún subtipo de Number, permitiendo tratar los valores como números y utilizar métodos como doubleValue() con seguridad en tiempo de compilación.

Una primera solución sencilla para modelar un Punto con coordenadas numéricas consiste en declarar directamente las coordenadas como Number. De este modo, el punto puede trabajar con distintos tipos de números (Integer, Double, etc.), pero se pierde información concreta del tipo, ya que ambos atributos se tratan siempre como Number. El chequeo de tipos es limitado, aunque suficiente para operar numéricamente mediante conversiones a double.

```java
public class Punto {
    private Number x;
    private Number y;

    public Punto(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(Punto otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Una segunda solución más robusta consiste en usar programación genérica con una restricción de tipo, definiendo el punto como Punto<T extends Number>. Esto permite reforzar el chequeo de tipos, ya que el compilador sabe exactamente con qué tipo numérico concreto trabaja cada instancia del punto. Así, se garantiza coherencia entre las coordenadas y se mantiene la flexibilidad sin perder seguridad.

```java
public class Punto<T extends Number> {
    private T x;
    private T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

Respecto al type erasure, en ambos casos el tipo final tras la compilación es Number. Aunque el compilador utiliza la información genérica para garantizar la seguridad en tiempo de compilación, dicha información se elimina posteriormente. En tiempo de ejecución, el código generado trabaja únicamente con Number, lo que confirma que los genéricos en Java aportan seguridad estática, pero no introducen nuevos tipos en tiempo de ejecución.


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

Ambas soluciones permiten trabajar con distintos tipos de números sin duplicar la clase Punto, pero no ofrecen el mismo nivel de chequeo de tipos. En la solución sin genéricos, donde las coordenadas se declaran directamente como Number, sí es posible crear un punto con coordenadas de distinto tipo, por ejemplo una coordenada entera (Integer) y la otra real (Double). El compilador lo permite porque ambas clases heredan de Number, aunque conceptualmente se estén mezclando tipos numéricos distintos dentro del mismo punto.

En la solución con genéricos (Punto<T extends Number>), el chequeo de tipos es más estricto. En este caso, ambas coordenadas deben ser del mismo tipo concreto, ya que el parámetro de tipo T se fija al crear la instancia. Por ejemplo, un Punto<Integer> solo puede tener coordenadas enteras, y un Punto<Double> solo coordenadas reales. El compilador impide mezclar tipos distintos en una misma instancia, reforzando la coherencia del modelo y evitando errores lógicos.

En cuanto al tipo devuelto por los métodos de acceso, también existe una diferencia clara. En la solución sin genéricos, el método getX devuelve siempre un Number, por lo que se pierde información sobre el tipo concreto y, si se necesita ese tipo específico, será necesario realizar conversiones adicionales. En cambio, en la solución con genéricos, getX devuelve el tipo T, es decir, el tipo numérico concreto con el que se ha instanciado el punto, lo que permite trabajar con él de forma segura y sin casting.

En conclusión, aunque ambas soluciones permiten flexibilidad, el uso de genéricos refuerza notablemente el chequeo de tipos, impide combinaciones incoherentes de datos y hace que la información de tipo sea explícita y segura en tiempo de compilación, aun cuando dicha información se elimine posteriormente debido al type erasure.

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

En el diseño original, el método distanciaA(Punto p) acepta cualquier objeto que implemente la interfaz Punto. Esto obliga a comprobar en tiempo de ejecución si el objeto recibido es del tipo correcto mediante instanceof y a realizar downcasting, lo que debilita el chequeo de tipos y desplaza los errores al tiempo de ejecución. Además, el diseño permite llamadas incoherentes, como calcular la distancia entre un Punto2D y un Punto3D, que conceptualmente no tiene sentido.
La programación genérica permite expresar esta restricción directamente en el sistema de tipos. Para ello, se puede parametrizar la interfaz Punto con un parámetro de tipo que represente el propio subtipo concreto (self-type pattern). De este modo, el método distanciaA se define para aceptar únicamente puntos del mismo tipo concreto, y el compilador garantiza esta propiedad sin necesidad de comprobaciones dinámicas.
Con este enfoque, cada implementación concreta (Punto2D, Punto3D) fija su propio tipo como parámetro genérico. La sobreescritura del método se realiza entonces de forma completamente segura, sin instanceof ni conversiones explícitas. Cualquier intento de calcular la distancia entre puntos de distinto tipo será rechazado en tiempo de compilación.
En consecuencia, el uso de genéricos refuerza el diseño, hace innecesarias las comprobaciones en ejecución y expresa de forma clara y precisa las restricciones del dominio del problema directamente en el código.

✅ Interfaz Punto genérica

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}
```

✅ Implementación Punto2D sin instanceof ni downcasting

```java
public class Punto2D implements Punto<Punto2D> {

    private final double x, y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

✅ Implementación Punto3D

```java
public class Punto3D implements Punto<Punto3D> {

    private final double x, y, z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D p) {
        double dx = x - p.x;
        double dy = y - p.y;
        double dz = z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```
Con este diseño, el compilador impide cualquier mezcla de tipos, garantiza coherencia semántica y elimina errores que antes solo podían detectarse en tiempo de ejecución.


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

Aunque String sea subtipo de Object, no significa que List<String> sea subtipo de List<Object>. En Java, los tipos genéricos son invariantes respecto a sus parámetros de tipo. Esto quiere decir que, aunque exista una relación de herencia entre String y Object, no existe esa relación entre List<String> y List<Object>. Permitirlo introduciría problemas de seguridad de tipos, ya que a través de una referencia List<Object> se podrían insertar objetos que no fuesen String en una lista que conceptualmente debería contener solo cadenas.

En cambio, los arrays sí son covariantes en Java, por lo que String[] sí es subtipo de Object[]. Esto permite asignar un array de String a una referencia de tipo Object[]. Sin embargo, esta decisión de diseño introduce un problema potencial en tiempo de ejecución: si se accede al array mediante la referencia Object[] y se intenta almacenar un objeto que no sea String, el compilador lo permite, pero en ejecución se produce una excepción ArrayStoreException. Es decir, el error no se detecta en compilación, sino en tiempo de ejecución.

Esta diferencia explica por qué Java decidió que los genéricos fuesen invariantes: para garantizar la seguridad de tipos en tiempo de compilación y evitar errores dinámicos como los que pueden aparecer con los arrays. En el caso de los genéricos, el compilador impide directamente usos incorrectos, mientras que los arrays confían en comprobaciones dinámicas que pueden fallar en ejecución.

A partir de estos ejemplos, se puede decir que un tipo genérico es covariante si permite sustituir un parámetro de tipo por uno de sus subtipos (A por B extends A), contravariante si permite sustituirlo por un supertipo, e invariante si no permite ninguna de las dos sustituciones. En Java, los arrays son covariantes, los genéricos son invariantes por defecto, y la variancia solo puede controlarse explícitamente mediante comodines (? extends y ? super) para introducir flexibilidad sin perder seguridad de tipos.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

n wildcard (?) es un comodín que se utiliza en los tipos genéricos de Java para relajar la invariancia y permitir cierta flexibilidad en el uso de tipos parametrizados. En lugar de especificar un tipo concreto, el wildcard indica que se acepta algún tipo desconocido, posiblemente con una restricción superior o inferior. De este modo, se puede recuperar covarianza o contravarianza de forma controlada, manteniendo la seguridad de tipos en compilación.

La forma List<? extends T> introduce covarianza. Significa que la lista contiene elementos de un tipo desconocido que es T o un subtipo de T. Este tipo de wildcard se utiliza cuando el código solo necesita leer elementos de la estructura, ya que se conoce que todos son, como mínimo, de tipo T. Sin embargo, no se permite añadir nuevos elementos (salvo null), porque el compilador no puede garantizar que el tipo concreto sea compatible.

Por el contrario, List<? super T> introduce contravarianza. En este caso, la lista contiene elementos de un tipo desconocido que es T o un supertipo de T. Este wildcard se utiliza cuando el objetivo principal es insertar elementos del tipo T en la estructura, ya que se garantiza que cualquier supertipo de T puede almacenarlos. A cambio, al recuperar elementos solo se tiene la garantía de que son de tipo Object.

En resumen, ? extends T se emplea cuando la estructura produce valores (lectura), y ? super T cuando la estructura consume valores (escritura). Esta idea se suele resumir con la regla PECS: Producer Extends, Consumer Super.

✅ Ejemplo (i): sumar una lista de números usando ? extends

```java
import java.util.List;

public class Utilidades {

    public static double sumar(List<? extends Number> numeros) {
        double suma = 0.0;
        for (Number n : numeros) {
            suma += n.doubleValue();
        }
        return suma;
    }
}
```
En este caso, el método acepta listas de Integer, Double, Float, etc., y se limita a leer los valores para calcular la suma.

✅ Ejemplo (ii): añadir enteros a una lista usando ? super

```java
import java.util.List;

public class Utilidades {

    public static void añadirEnteros(List<? super Integer> lista) {
        lista.add(1);
        lista.add(2);
        lista.add(3);
    }
}
```

Aquí, el método puede añadir valores de tipo Integer a listas de Integer, Number o Object, ya que todas ellas son supertipos válidos de Integer. El uso del wildcard garantiza la seguridad sin perder flexibilidad.

Estos ejemplos muestran cómo los wildcards permiten recuperar covarianza y contravarianza en Java de forma segura, resolviendo las limitaciones derivadas de la invariancia de los genéricos y evitando errores en tiempo de ejecución.
